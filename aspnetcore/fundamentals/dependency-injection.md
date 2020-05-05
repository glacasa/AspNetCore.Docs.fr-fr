---
title: Injection de dépendances dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core implémente l’injection de dépendances et comment l’utiliser.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 8a4ee8bee09b3d6e9de932dab17bbc5c6494a492
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767523"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="7ba62-103">Injection de dépendances dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba62-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="7ba62-104">Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="7ba62-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ba62-105">ASP.NET Core prend en charge le modèle de conception de logiciel avec injection de dépendances (DI), une technique permettant d’obtenir une [inversion de contrôle (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="7ba62-106">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="7ba62-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ba62-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="7ba62-108">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7ba62-108">Overview of dependency injection</span></span>

<span data-ttu-id="7ba62-109">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7ba62-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="7ba62-110">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="7ba62-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="7ba62-111">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="7ba62-112">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="7ba62-113">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="7ba62-114">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="7ba62-115">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="7ba62-116">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="7ba62-117">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="7ba62-118">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="7ba62-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="7ba62-119">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="7ba62-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="7ba62-120">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="7ba62-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="7ba62-121">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="7ba62-122">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="7ba62-123">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="7ba62-124">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="7ba62-125">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="7ba62-126">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7ba62-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="7ba62-127">Dans l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="7ba62-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="7ba62-128">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="7ba62-129">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="7ba62-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="7ba62-130">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="7ba62-131">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="7ba62-132">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="7ba62-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="7ba62-133">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="7ba62-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="7ba62-134">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="7ba62-135">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7ba62-136">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7ba62-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="7ba62-137">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="7ba62-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="7ba62-138">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="7ba62-139">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="7ba62-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="7ba62-140">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="7ba62-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="7ba62-141">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="7ba62-142">Par exemple, `services.AddMvc()` ajoute les services dont Razor Pages et MVC ont besoin.</span><span class="sxs-lookup"><span data-stu-id="7ba62-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="7ba62-143">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="7ba62-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="7ba62-144">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="7ba62-145">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="7ba62-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="7ba62-146">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="7ba62-147">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="7ba62-148">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="7ba62-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="7ba62-149">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="7ba62-149">Services injected into Startup</span></span>

<span data-ttu-id="7ba62-150">Seuls les types de service suivants peuvent être injectés dans `Startup` le constructeur lors de l’utilisation de<xref:Microsoft.Extensions.Hosting.IHostBuilder>l’hôte générique () :</span><span class="sxs-lookup"><span data-stu-id="7ba62-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="7ba62-151">Les services peuvent être injectés `Startup.Configure`dans :</span><span class="sxs-lookup"><span data-stu-id="7ba62-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="7ba62-152">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7ba62-153">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="7ba62-153">Framework-provided services</span></span>

<span data-ttu-id="7ba62-154">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="7ba62-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="7ba62-155">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="7ba62-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="7ba62-156">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7ba62-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="7ba62-157">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="7ba62-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="7ba62-158">Type de service</span><span class="sxs-lookup"><span data-stu-id="7ba62-158">Service Type</span></span> | <span data-ttu-id="7ba62-159">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="7ba62-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="7ba62-160">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="7ba62-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="7ba62-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="7ba62-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="7ba62-164">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="7ba62-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="7ba62-166">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="7ba62-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="7ba62-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="7ba62-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="7ba62-170">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="7ba62-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="7ba62-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="7ba62-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="7ba62-174">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="7ba62-174">Register additional services with extension methods</span></span>

<span data-ttu-id="7ba62-175">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="7ba62-176">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et :</span><span class="sxs-lookup"><span data-stu-id="7ba62-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="7ba62-177">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="7ba62-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="7ba62-178">Durées de service</span><span class="sxs-lookup"><span data-stu-id="7ba62-178">Service lifetimes</span></span>

<span data-ttu-id="7ba62-179">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="7ba62-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="7ba62-180">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="7ba62-181">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-181">Transient</span></span>

<span data-ttu-id="7ba62-182">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="7ba62-183">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="7ba62-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="7ba62-184">Délimité</span><span class="sxs-lookup"><span data-stu-id="7ba62-184">Scoped</span></span>

<span data-ttu-id="7ba62-185">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="7ba62-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="7ba62-186">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="7ba62-187">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="7ba62-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="7ba62-188">Pour plus d’informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="7ba62-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-189">Singleton</span></span>

<span data-ttu-id="7ba62-190">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="7ba62-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="7ba62-191">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="7ba62-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="7ba62-192">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="7ba62-193">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="7ba62-194">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="7ba62-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7ba62-195">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="7ba62-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="7ba62-196">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="7ba62-196">Service registration methods</span></span>

<span data-ttu-id="7ba62-197">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7ba62-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="7ba62-198">Méthode</span><span class="sxs-lookup"><span data-stu-id="7ba62-198">Method</span></span> | <span data-ttu-id="7ba62-199">Automatique</span><span class="sxs-lookup"><span data-stu-id="7ba62-199">Automatic</span></span><br><span data-ttu-id="7ba62-200">object</span><span class="sxs-lookup"><span data-stu-id="7ba62-200">object</span></span><br><span data-ttu-id="7ba62-201">suppression</span><span class="sxs-lookup"><span data-stu-id="7ba62-201">disposal</span></span> | <span data-ttu-id="7ba62-202">Multiple</span><span class="sxs-lookup"><span data-stu-id="7ba62-202">Multiple</span></span><br><span data-ttu-id="7ba62-203">implémentations</span><span class="sxs-lookup"><span data-stu-id="7ba62-203">implementations</span></span> | <span data-ttu-id="7ba62-204">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="7ba62-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="7ba62-205">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7ba62-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="7ba62-206">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-206">Yes</span></span> | <span data-ttu-id="7ba62-207">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-207">Yes</span></span> | <span data-ttu-id="7ba62-208">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="7ba62-209">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7ba62-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="7ba62-210">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-210">Yes</span></span> | <span data-ttu-id="7ba62-211">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-211">Yes</span></span> | <span data-ttu-id="7ba62-212">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="7ba62-213">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7ba62-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="7ba62-214">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-214">Yes</span></span> | <span data-ttu-id="7ba62-215">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-215">No</span></span> | <span data-ttu-id="7ba62-216">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="7ba62-217">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7ba62-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="7ba62-218">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-218">No</span></span> | <span data-ttu-id="7ba62-219">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-219">Yes</span></span> | <span data-ttu-id="7ba62-220">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="7ba62-221">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7ba62-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="7ba62-222">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-222">No</span></span> | <span data-ttu-id="7ba62-223">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-223">No</span></span> | <span data-ttu-id="7ba62-224">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-224">Yes</span></span> |

<span data-ttu-id="7ba62-225">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="7ba62-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="7ba62-226">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="7ba62-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="7ba62-227">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="7ba62-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="7ba62-228">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="7ba62-229">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="7ba62-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="7ba62-230">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="7ba62-231">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="7ba62-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="7ba62-232">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="7ba62-233">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="7ba62-234">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="7ba62-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="7ba62-235">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="7ba62-236">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="7ba62-237">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="7ba62-238">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="7ba62-238">Constructor injection behavior</span></span>

<span data-ttu-id="7ba62-239">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="7ba62-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="7ba62-241">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="7ba62-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="7ba62-242">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7ba62-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="7ba62-243">Lorsque les `IServiceProvider` services sont résolus `ActivatorUtilities`par ou, l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="7ba62-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="7ba62-244">Lorsque les services sont résolus par `ActivatorUtilities`, l’injection de [constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="7ba62-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="7ba62-245">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="7ba62-246">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7ba62-246">Entity Framework contexts</span></span>

<span data-ttu-id="7ba62-247">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="7ba62-248">La durée de vie par défaut est limitée si aucune durée de vie n’est spécifiée par une surcharge [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’inscription du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="7ba62-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="7ba62-249">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="7ba62-250">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="7ba62-250">Lifetime and registration options</span></span>

<span data-ttu-id="7ba62-251">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="7ba62-252">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="7ba62-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="7ba62-253">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="7ba62-254">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="7ba62-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="7ba62-255">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="7ba62-256">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="7ba62-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="7ba62-257">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="7ba62-258">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="7ba62-259">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="7ba62-260">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="7ba62-261">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="7ba62-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="7ba62-262">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="7ba62-263">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="7ba62-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="7ba62-264">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="7ba62-265">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="7ba62-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="7ba62-266">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="7ba62-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="7ba62-267">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="7ba62-268">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="7ba62-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="7ba62-269">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="7ba62-270">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="7ba62-270">**First request:**</span></span>

<span data-ttu-id="7ba62-271">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7ba62-271">Controller operations:</span></span>

<span data-ttu-id="7ba62-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="7ba62-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="7ba62-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7ba62-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7ba62-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-275">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-276">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-276">`OperationService` operations:</span></span>

<span data-ttu-id="7ba62-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="7ba62-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="7ba62-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7ba62-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7ba62-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-281">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="7ba62-281">**Second request:**</span></span>

<span data-ttu-id="7ba62-282">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7ba62-282">Controller operations:</span></span>

<span data-ttu-id="7ba62-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="7ba62-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="7ba62-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7ba62-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7ba62-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-286">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-287">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-287">`OperationService` operations:</span></span>

<span data-ttu-id="7ba62-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="7ba62-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="7ba62-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7ba62-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7ba62-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-292">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="7ba62-293">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="7ba62-293">*Transient* objects are always different.</span></span> <span data-ttu-id="7ba62-294">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="7ba62-295">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="7ba62-296">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="7ba62-297">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou `Startup.ConfigureServices`non dans.</span><span class="sxs-lookup"><span data-stu-id="7ba62-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="7ba62-298">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="7ba62-298">Call services from main</span></span>

<span data-ttu-id="7ba62-299">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="7ba62-300">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="7ba62-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="7ba62-301">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="7ba62-302">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="7ba62-302">Scope validation</span></span>

<span data-ttu-id="7ba62-303">Lorsque l’application s’exécute dans l’environnement de développement et appelle [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) pour générer l’hôte, le fournisseur de services par défaut effectue des vérifications pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="7ba62-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="7ba62-304">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="7ba62-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="7ba62-305">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="7ba62-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="7ba62-306">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="7ba62-307">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7ba62-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="7ba62-308">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="7ba62-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="7ba62-309">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="7ba62-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="7ba62-310">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="7ba62-311">Pour plus d’informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="7ba62-312">Services de requête</span><span class="sxs-lookup"><span data-stu-id="7ba62-312">Request Services</span></span>

<span data-ttu-id="7ba62-313">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="7ba62-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="7ba62-314">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="7ba62-315">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="7ba62-316">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="7ba62-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="7ba62-317">Au lieu de cela, demandez les types que les classes requièrent via les constructeurs de classe et autorisez l’infrastructure à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="7ba62-318">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="7ba62-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba62-319">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="7ba62-320">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7ba62-320">Design services for dependency injection</span></span>

<span data-ttu-id="7ba62-321">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="7ba62-321">Best practices are to:</span></span>

* <span data-ttu-id="7ba62-322">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="7ba62-323">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="7ba62-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="7ba62-324">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="7ba62-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="7ba62-325">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="7ba62-326">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="7ba62-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="7ba62-327">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="7ba62-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="7ba62-328">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="7ba62-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="7ba62-329">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="7ba62-330">N’oubliez pas que les classes du modèle de page Razor Pages et les classes du contrôleur MVC doivent se concentrer sur les problèmes d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7ba62-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="7ba62-331">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="7ba62-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="7ba62-332">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="7ba62-332">Disposal of services</span></span>

<span data-ttu-id="7ba62-333">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="7ba62-334">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7ba62-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="7ba62-335">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="7ba62-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="7ba62-336">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7ba62-336">In the following example:</span></span>

* <span data-ttu-id="7ba62-337">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="7ba62-338">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7ba62-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="7ba62-339">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="7ba62-340">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7ba62-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="7ba62-341">Pour une description des options de suppression de service, consultez [quatre façons de supprimer des IDisposable dans ASP.net Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="7ba62-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="7ba62-342">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="7ba62-342">Default service container replacement</span></span>

<span data-ttu-id="7ba62-343">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="7ba62-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="7ba62-344">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="7ba62-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="7ba62-345">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="7ba62-345">Property injection</span></span>
* <span data-ttu-id="7ba62-346">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="7ba62-346">Injection based on name</span></span>
* <span data-ttu-id="7ba62-347">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="7ba62-347">Child containers</span></span>
* <span data-ttu-id="7ba62-348">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="7ba62-348">Custom lifetime management</span></span>
* <span data-ttu-id="7ba62-349">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="7ba62-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="7ba62-350">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="7ba62-350">Convention-based registration</span></span>

<span data-ttu-id="7ba62-351">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="7ba62-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="7ba62-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="7ba62-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="7ba62-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="7ba62-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="7ba62-354">Grace</span><span class="sxs-lookup"><span data-stu-id="7ba62-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="7ba62-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="7ba62-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="7ba62-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="7ba62-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="7ba62-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="7ba62-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="7ba62-358">Unity</span><span class="sxs-lookup"><span data-stu-id="7ba62-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="7ba62-359">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="7ba62-359">Thread safety</span></span>

<span data-ttu-id="7ba62-360">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="7ba62-361">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="7ba62-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="7ba62-362">La méthode de fabrique d’un service individuel, comme le deuxième argument de [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="7ba62-363">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="7ba62-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="7ba62-364">Recommandations</span><span class="sxs-lookup"><span data-stu-id="7ba62-364">Recommendations</span></span>

* <span data-ttu-id="7ba62-365">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7ba62-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="7ba62-366">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="7ba62-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="7ba62-367">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="7ba62-368">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="7ba62-369">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="7ba62-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7ba62-370">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7ba62-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="7ba62-371">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="7ba62-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="7ba62-372">Évitez l’accès statique aux services (par exemple avec [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) à utiliser ailleurs).</span><span class="sxs-lookup"><span data-stu-id="7ba62-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="7ba62-373">Évitez d’utiliser le *modèle de localisation de service*.</span><span class="sxs-lookup"><span data-stu-id="7ba62-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="7ba62-374">Par exemple, n’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service si vous pouvez utiliser l’injection de dépendance à la place :</span><span class="sxs-lookup"><span data-stu-id="7ba62-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="7ba62-375">**Correcte**</span><span class="sxs-lookup"><span data-stu-id="7ba62-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="7ba62-376">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="7ba62-376">**Correct**:</span></span>

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

* <span data-ttu-id="7ba62-377">Une autre variante du localisateur de service à éviter est l’injection d’une fabrique qui résout les dépendances au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="7ba62-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="7ba62-378">Ces deux pratiques combinent des stratégies [Inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="7ba62-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="7ba62-379">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="7ba62-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="7ba62-380">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="7ba62-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="7ba62-381">Les exceptions sont rares et représentent principalement des cas spéciaux dans le framework lui-même.</span><span class="sxs-lookup"><span data-stu-id="7ba62-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="7ba62-382">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="7ba62-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="7ba62-383">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="7ba62-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ba62-384">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7ba62-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="7ba62-385">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="7ba62-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="7ba62-386">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="7ba62-386">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="7ba62-387">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="7ba62-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="7ba62-388">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba62-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ba62-389">ASP.NET Core prend en charge le modèle de conception de logiciel avec injection de dépendances (DI), une technique permettant d’obtenir une [inversion de contrôle (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="7ba62-390">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="7ba62-391">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ba62-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="7ba62-392">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7ba62-392">Overview of dependency injection</span></span>

<span data-ttu-id="7ba62-393">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7ba62-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="7ba62-394">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="7ba62-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="7ba62-395">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="7ba62-396">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="7ba62-397">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="7ba62-398">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="7ba62-399">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="7ba62-400">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="7ba62-401">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="7ba62-402">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="7ba62-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="7ba62-403">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="7ba62-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="7ba62-404">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="7ba62-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="7ba62-405">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="7ba62-406">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="7ba62-407">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="7ba62-408">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="7ba62-409">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="7ba62-410">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7ba62-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="7ba62-411">Dans l’[exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="7ba62-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="7ba62-412">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="7ba62-413">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="7ba62-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="7ba62-414">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="7ba62-415">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="7ba62-416">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="7ba62-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="7ba62-417">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="7ba62-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="7ba62-418">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="7ba62-419">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7ba62-420">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7ba62-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="7ba62-421">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="7ba62-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="7ba62-422">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="7ba62-423">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="7ba62-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="7ba62-424">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="7ba62-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="7ba62-425">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="7ba62-426">Par exemple, `services.AddMvc()` ajoute les services dont Razor Pages et MVC ont besoin.</span><span class="sxs-lookup"><span data-stu-id="7ba62-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="7ba62-427">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="7ba62-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="7ba62-428">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="7ba62-429">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="7ba62-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="7ba62-430">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="7ba62-431">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="7ba62-432">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="7ba62-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="7ba62-433">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="7ba62-433">Services injected into Startup</span></span>

<span data-ttu-id="7ba62-434">Seuls les types de service suivants peuvent être injectés dans `Startup` le constructeur lors de l’utilisation de<xref:Microsoft.Extensions.Hosting.IHostBuilder>l’hôte générique () :</span><span class="sxs-lookup"><span data-stu-id="7ba62-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="7ba62-435">Les services peuvent être injectés `Startup.Configure`dans :</span><span class="sxs-lookup"><span data-stu-id="7ba62-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="7ba62-436">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7ba62-437">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="7ba62-437">Framework-provided services</span></span>

<span data-ttu-id="7ba62-438">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="7ba62-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="7ba62-439">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="7ba62-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="7ba62-440">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7ba62-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="7ba62-441">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="7ba62-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="7ba62-442">Type de service</span><span class="sxs-lookup"><span data-stu-id="7ba62-442">Service Type</span></span> | <span data-ttu-id="7ba62-443">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="7ba62-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="7ba62-444">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="7ba62-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="7ba62-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="7ba62-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="7ba62-448">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="7ba62-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="7ba62-450">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="7ba62-451">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="7ba62-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="7ba62-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="7ba62-454">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="7ba62-455">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="7ba62-456">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="7ba62-457">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="7ba62-458">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="7ba62-458">Register additional services with extension methods</span></span>

<span data-ttu-id="7ba62-459">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="7ba62-460">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> [\<AddDbContext TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et :</span><span class="sxs-lookup"><span data-stu-id="7ba62-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="7ba62-461">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="7ba62-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="7ba62-462">Durées de service</span><span class="sxs-lookup"><span data-stu-id="7ba62-462">Service lifetimes</span></span>

<span data-ttu-id="7ba62-463">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="7ba62-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="7ba62-464">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="7ba62-465">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7ba62-465">Transient</span></span>

<span data-ttu-id="7ba62-466">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="7ba62-467">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="7ba62-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="7ba62-468">Délimité</span><span class="sxs-lookup"><span data-stu-id="7ba62-468">Scoped</span></span>

<span data-ttu-id="7ba62-469">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="7ba62-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="7ba62-470">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="7ba62-471">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="7ba62-471">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="7ba62-472">Pour plus d’informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="7ba62-473">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ba62-473">Singleton</span></span>

<span data-ttu-id="7ba62-474">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="7ba62-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="7ba62-475">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="7ba62-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="7ba62-476">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="7ba62-477">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="7ba62-478">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="7ba62-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7ba62-479">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="7ba62-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="7ba62-480">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="7ba62-480">Service registration methods</span></span>

<span data-ttu-id="7ba62-481">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7ba62-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="7ba62-482">Méthode</span><span class="sxs-lookup"><span data-stu-id="7ba62-482">Method</span></span> | <span data-ttu-id="7ba62-483">Automatique</span><span class="sxs-lookup"><span data-stu-id="7ba62-483">Automatic</span></span><br><span data-ttu-id="7ba62-484">object</span><span class="sxs-lookup"><span data-stu-id="7ba62-484">object</span></span><br><span data-ttu-id="7ba62-485">suppression</span><span class="sxs-lookup"><span data-stu-id="7ba62-485">disposal</span></span> | <span data-ttu-id="7ba62-486">Multiple</span><span class="sxs-lookup"><span data-stu-id="7ba62-486">Multiple</span></span><br><span data-ttu-id="7ba62-487">implémentations</span><span class="sxs-lookup"><span data-stu-id="7ba62-487">implementations</span></span> | <span data-ttu-id="7ba62-488">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="7ba62-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="7ba62-489">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7ba62-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="7ba62-490">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-490">Yes</span></span> | <span data-ttu-id="7ba62-491">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-491">Yes</span></span> | <span data-ttu-id="7ba62-492">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="7ba62-493">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7ba62-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="7ba62-494">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-494">Yes</span></span> | <span data-ttu-id="7ba62-495">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-495">Yes</span></span> | <span data-ttu-id="7ba62-496">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="7ba62-497">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7ba62-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="7ba62-498">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-498">Yes</span></span> | <span data-ttu-id="7ba62-499">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-499">No</span></span> | <span data-ttu-id="7ba62-500">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="7ba62-501">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7ba62-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="7ba62-502">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-502">No</span></span> | <span data-ttu-id="7ba62-503">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-503">Yes</span></span> | <span data-ttu-id="7ba62-504">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="7ba62-505">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7ba62-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="7ba62-506">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-506">No</span></span> | <span data-ttu-id="7ba62-507">Non</span><span class="sxs-lookup"><span data-stu-id="7ba62-507">No</span></span> | <span data-ttu-id="7ba62-508">Oui</span><span class="sxs-lookup"><span data-stu-id="7ba62-508">Yes</span></span> |

<span data-ttu-id="7ba62-509">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="7ba62-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="7ba62-510">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="7ba62-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="7ba62-511">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="7ba62-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="7ba62-512">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="7ba62-513">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="7ba62-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="7ba62-514">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="7ba62-515">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="7ba62-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="7ba62-516">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="7ba62-517">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="7ba62-518">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="7ba62-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="7ba62-519">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="7ba62-520">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="7ba62-521">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="7ba62-522">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="7ba62-522">Constructor injection behavior</span></span>

<span data-ttu-id="7ba62-523">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="7ba62-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="7ba62-525">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="7ba62-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="7ba62-526">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7ba62-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="7ba62-527">Lorsque les `IServiceProvider` services sont résolus `ActivatorUtilities`par ou, l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="7ba62-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="7ba62-528">Lorsque les services sont résolus par `ActivatorUtilities`, l’injection de [constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="7ba62-528">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="7ba62-529">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="7ba62-530">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7ba62-530">Entity Framework contexts</span></span>

<span data-ttu-id="7ba62-531">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="7ba62-532">La durée de vie par défaut est limitée si aucune durée de vie n’est spécifiée par une surcharge [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’inscription du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="7ba62-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="7ba62-533">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="7ba62-534">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="7ba62-534">Lifetime and registration options</span></span>

<span data-ttu-id="7ba62-535">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="7ba62-536">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="7ba62-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="7ba62-537">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="7ba62-538">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="7ba62-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="7ba62-539">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="7ba62-540">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="7ba62-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="7ba62-541">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="7ba62-542">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="7ba62-543">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="7ba62-544">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="7ba62-545">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="7ba62-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="7ba62-546">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="7ba62-547">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="7ba62-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="7ba62-548">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="7ba62-549">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="7ba62-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="7ba62-550">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="7ba62-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="7ba62-551">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="7ba62-552">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="7ba62-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="7ba62-553">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="7ba62-554">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="7ba62-554">**First request:**</span></span>

<span data-ttu-id="7ba62-555">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7ba62-555">Controller operations:</span></span>

<span data-ttu-id="7ba62-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="7ba62-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="7ba62-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7ba62-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7ba62-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-559">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-560">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-560">`OperationService` operations:</span></span>

<span data-ttu-id="7ba62-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="7ba62-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="7ba62-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7ba62-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7ba62-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-564">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-565">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="7ba62-565">**Second request:**</span></span>

<span data-ttu-id="7ba62-566">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7ba62-566">Controller operations:</span></span>

<span data-ttu-id="7ba62-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="7ba62-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="7ba62-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7ba62-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7ba62-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-570">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-571">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-571">`OperationService` operations:</span></span>

<span data-ttu-id="7ba62-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="7ba62-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="7ba62-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7ba62-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7ba62-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7ba62-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7ba62-575">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7ba62-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7ba62-576">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="7ba62-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="7ba62-577">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="7ba62-577">*Transient* objects are always different.</span></span> <span data-ttu-id="7ba62-578">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="7ba62-579">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="7ba62-580">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="7ba62-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="7ba62-581">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou `Startup.ConfigureServices`non dans.</span><span class="sxs-lookup"><span data-stu-id="7ba62-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="7ba62-582">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="7ba62-582">Call services from main</span></span>

<span data-ttu-id="7ba62-583">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="7ba62-584">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="7ba62-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="7ba62-585">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="7ba62-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="7ba62-586">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="7ba62-586">Scope validation</span></span>

<span data-ttu-id="7ba62-587">Quand l’application s’exécute dans l’environnement de développement, le fournisseur de services par défaut effectue des contrôles pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="7ba62-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="7ba62-588">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="7ba62-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="7ba62-589">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="7ba62-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="7ba62-590">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="7ba62-591">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7ba62-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="7ba62-592">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="7ba62-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="7ba62-593">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="7ba62-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="7ba62-594">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="7ba62-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="7ba62-595">Pour plus d’informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="7ba62-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="7ba62-596">Services de requête</span><span class="sxs-lookup"><span data-stu-id="7ba62-596">Request Services</span></span>

<span data-ttu-id="7ba62-597">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="7ba62-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="7ba62-598">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="7ba62-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="7ba62-599">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="7ba62-600">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="7ba62-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="7ba62-601">Demandez plutôt les types nécessaires à la classe via des constructeurs de classe et autorisez le framework à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="7ba62-602">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="7ba62-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba62-603">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba62-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="7ba62-604">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7ba62-604">Design services for dependency injection</span></span>

<span data-ttu-id="7ba62-605">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="7ba62-605">Best practices are to:</span></span>

* <span data-ttu-id="7ba62-606">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="7ba62-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="7ba62-607">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="7ba62-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="7ba62-608">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="7ba62-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="7ba62-609">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="7ba62-610">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="7ba62-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="7ba62-611">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="7ba62-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="7ba62-612">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="7ba62-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="7ba62-613">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="7ba62-614">Gardez à l’esprit Razor que les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7ba62-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="7ba62-615">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="7ba62-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="7ba62-616">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="7ba62-616">Disposal of services</span></span>

<span data-ttu-id="7ba62-617">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="7ba62-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="7ba62-618">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7ba62-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="7ba62-619">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="7ba62-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="7ba62-620">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7ba62-620">In the following example:</span></span>

* <span data-ttu-id="7ba62-621">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7ba62-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="7ba62-622">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7ba62-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="7ba62-623">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="7ba62-624">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7ba62-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="7ba62-625">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="7ba62-625">Default service container replacement</span></span>

<span data-ttu-id="7ba62-626">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="7ba62-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="7ba62-627">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="7ba62-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="7ba62-628">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="7ba62-628">Property injection</span></span>
* <span data-ttu-id="7ba62-629">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="7ba62-629">Injection based on name</span></span>
* <span data-ttu-id="7ba62-630">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="7ba62-630">Child containers</span></span>
* <span data-ttu-id="7ba62-631">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="7ba62-631">Custom lifetime management</span></span>
* <span data-ttu-id="7ba62-632">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="7ba62-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="7ba62-633">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="7ba62-633">Convention-based registration</span></span>

<span data-ttu-id="7ba62-634">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="7ba62-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="7ba62-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="7ba62-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="7ba62-636">DryIoc</span><span class="sxs-lookup"><span data-stu-id="7ba62-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="7ba62-637">Grace</span><span class="sxs-lookup"><span data-stu-id="7ba62-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="7ba62-638">LightInject</span><span class="sxs-lookup"><span data-stu-id="7ba62-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="7ba62-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="7ba62-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="7ba62-640">Stashbox</span><span class="sxs-lookup"><span data-stu-id="7ba62-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="7ba62-641">Unity</span><span class="sxs-lookup"><span data-stu-id="7ba62-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="7ba62-642">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="7ba62-642">Thread safety</span></span>

<span data-ttu-id="7ba62-643">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="7ba62-644">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="7ba62-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="7ba62-645">La méthode de fabrique d’un service individuel, comme le deuxième argument de [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7ba62-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="7ba62-646">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="7ba62-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="7ba62-647">Recommandations</span><span class="sxs-lookup"><span data-stu-id="7ba62-647">Recommendations</span></span>

* <span data-ttu-id="7ba62-648">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7ba62-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="7ba62-649">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="7ba62-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="7ba62-650">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="7ba62-651">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7ba62-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="7ba62-652">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="7ba62-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7ba62-653">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7ba62-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="7ba62-654">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="7ba62-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="7ba62-655">Évitez l’accès statique aux services (par exemple avec [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) à utiliser ailleurs).</span><span class="sxs-lookup"><span data-stu-id="7ba62-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="7ba62-656">Évitez d’utiliser le *modèle de localisation de service*, qui combine les stratégies [d’inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="7ba62-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="7ba62-657">N’appelez <xref:System.IServiceProvider.GetService*> pas pour obtenir une instance de service lorsque vous pouvez utiliser l’injection de code à la place :</span><span class="sxs-lookup"><span data-stu-id="7ba62-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="7ba62-658">**Correcte**</span><span class="sxs-lookup"><span data-stu-id="7ba62-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="7ba62-659">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="7ba62-659">**Correct**:</span></span>

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

  * <span data-ttu-id="7ba62-660">Évitez d’injecter une fabrique qui résout les dépendances au <xref:System.IServiceProvider.GetService*>moment de l’exécution à l’aide de.</span><span class="sxs-lookup"><span data-stu-id="7ba62-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="7ba62-661">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="7ba62-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="7ba62-662">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="7ba62-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="7ba62-663">Les exceptions sont rares et représentent principalement des cas spéciaux dans le framework lui-même.</span><span class="sxs-lookup"><span data-stu-id="7ba62-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="7ba62-664">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="7ba62-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="7ba62-665">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="7ba62-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ba62-666">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7ba62-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="7ba62-667">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="7ba62-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="7ba62-668">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="7ba62-668">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="7ba62-669">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="7ba62-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="7ba62-670">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba62-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
