---
title: Injection de dépendances dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core implémente l’injection de dépendances et comment l’utiliser.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 98c43eda002abc96fe3c2f031c429ccaa70cee3e
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102781"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="7d34c-103">Injection de dépendances dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d34c-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7d34c-104">Par [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="7d34c-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="7d34c-105">ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="7d34c-106">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="7d34c-107">Pour plus d’informations sur l’injection de dépendances d’options, consultez <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="7d34c-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="7d34c-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7d34c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="7d34c-109">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7d34c-109">Overview of dependency injection</span></span>

<span data-ttu-id="7d34c-110">Une *dépendance* est un objet dont dépend un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7d34c-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="7d34c-111">Examinez la `MyDependency` classe suivante avec une `WriteMessage` méthode dont dépendent d’autres classes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="7d34c-112">Une classe peut créer une instance de la `MyDependency` classe pour utiliser sa `WriteMessage` méthode.</span><span class="sxs-lookup"><span data-stu-id="7d34c-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="7d34c-113">Dans l’exemple suivant, la `MyDependency` classe est une dépendance de la `IndexModel` classe :</span><span class="sxs-lookup"><span data-stu-id="7d34c-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="7d34c-114">La classe crée et dépend directement de la `MyDependency` classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="7d34c-115">Les dépendances de code, comme dans l’exemple précédent, sont problématiques et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="7d34c-116">Pour remplacer `MyDependency` par une implémentation différente, vous `IndexModel` devez modifier la classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="7d34c-117">Si `MyDependency` a des dépendances, ils doivent également être configurés par la `IndexModel` classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="7d34c-118">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="7d34c-119">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="7d34c-120">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="7d34c-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="7d34c-121">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="7d34c-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="7d34c-122">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="7d34c-123">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="7d34c-124">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="7d34c-125">Les services sont généralement inscrits dans la méthode de l’application `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="7d34c-126">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7d34c-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="7d34c-127">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="7d34c-128">Dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l' `IMyDependency` interface définit la `WriteMessage` méthode :</span><span class="sxs-lookup"><span data-stu-id="7d34c-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="7d34c-129">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="7d34c-130">L’exemple d’application inscrit le `IMyDependency` service avec le type concret `MyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="7d34c-131">La <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> méthode enregistre le service avec une durée de vie limitée, la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="7d34c-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="7d34c-132">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="7d34c-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="7d34c-133">Dans l’exemple d’application, le `IMyDependency` service est demandé et utilisé pour appeler la `WriteMessage` méthode :</span><span class="sxs-lookup"><span data-stu-id="7d34c-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="7d34c-134">En utilisant le modèle DI, le contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7d34c-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="7d34c-135">N’utilise pas le type concret `MyDependency` , mais uniquement l' `IMyDependency` interface qu’il implémente.</span><span class="sxs-lookup"><span data-stu-id="7d34c-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="7d34c-136">Cela facilite la modification de l’implémentation que le contrôleur utilise sans modifier le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="7d34c-137">Ne crée pas d’instance de `MyDependency` , elle est créée par le conteneur di.</span><span class="sxs-lookup"><span data-stu-id="7d34c-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="7d34c-138">L’implémentation de l' `IMyDependency` interface peut être améliorée à l’aide de l’API de journalisation intégrée :</span><span class="sxs-lookup"><span data-stu-id="7d34c-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="7d34c-139">La méthode mise à jour `ConfigureServices` inscrit la nouvelle `IMyDependency` implémentation :</span><span class="sxs-lookup"><span data-stu-id="7d34c-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="7d34c-140">`MyDependency2` dépend de <xref:Microsoft.Extensions.Logging.ILogger%601> , qu’il demande dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="7d34c-141">`ILogger<TCategoryName>` est un [service fourni par le Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="7d34c-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="7d34c-142">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="7d34c-143">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="7d34c-144">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="7d34c-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="7d34c-145">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="7d34c-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="7d34c-146">Le conteneur se résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui évite d’avoir à inscrire chaque [type construit (Générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="7d34c-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="7d34c-147">Dans la terminologie d’injection de dépendances, un service :</span><span class="sxs-lookup"><span data-stu-id="7d34c-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="7d34c-148">Est généralement un objet qui fournit un service à d’autres objets, tels que le `IMyDependency` service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="7d34c-149">N’est pas associé à un service Web, bien que le service puisse utiliser un service Web.</span><span class="sxs-lookup"><span data-stu-id="7d34c-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="7d34c-150">Le Framework fournit un système de [journalisation](xref:fundamentals/logging/index) robuste.</span><span class="sxs-lookup"><span data-stu-id="7d34c-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="7d34c-151">Les `IMyDependency` implémentations présentées dans les exemples précédents ont été écrites pour illustrer la di de base, et non pour implémenter la journalisation.</span><span class="sxs-lookup"><span data-stu-id="7d34c-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="7d34c-152">La plupart des applications n’ont pas besoin d’écrire des enregistreurs.</span><span class="sxs-lookup"><span data-stu-id="7d34c-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="7d34c-153">Le code suivant illustre l’utilisation de la journalisation par défaut, qui ne nécessite pas l’inscription de services dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="7d34c-154">À l’aide du code précédent, il n’est pas nécessaire de mettre à jour `ConfigureServices` , car la [journalisation](xref:fundamentals/logging/index) est assurée par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7d34c-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="7d34c-155">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="7d34c-155">Services injected into Startup</span></span>

<span data-ttu-id="7d34c-156">Les services peuvent être injectés dans le `Startup` constructeur et la `Startup.Configure` méthode.</span><span class="sxs-lookup"><span data-stu-id="7d34c-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="7d34c-157">Seuls les services suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="7d34c-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="7d34c-158">Tout service enregistré avec le conteneur DI peut être injecté dans la `Startup.Configure` méthode :</span><span class="sxs-lookup"><span data-stu-id="7d34c-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="7d34c-159">Pour plus d’informations, consultez <xref:fundamentals/startup> et [configuration de l’accès au démarrage](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="7d34c-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="7d34c-160">Inscrire des groupes de services avec des méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="7d34c-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="7d34c-161">L’infrastructure de ASP.NET Core utilise une convention pour inscrire un groupe de services connexes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="7d34c-162">La Convention consiste à utiliser une `Add{GROUP_NAME}` méthode d’extension unique pour inscrire tous les services requis par une fonctionnalité de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7d34c-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="7d34c-163">Par exemple, la méthode d’extension <Microsoft. extensions. DependencyInjection. MvcServiceCollectionExtensions. AddControllers> inscrit les services requis pour les contrôleurs MVC.</span><span class="sxs-lookup"><span data-stu-id="7d34c-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="7d34c-164">Le code suivant est généré par le Razor modèle pages à l’aide de comptes d’utilisateur individuels et montre comment ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="7d34c-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="7d34c-165">Durées de service</span><span class="sxs-lookup"><span data-stu-id="7d34c-165">Service lifetimes</span></span>

<span data-ttu-id="7d34c-166">Les services peuvent être enregistrés avec l’une des durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="7d34c-167">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-167">Transient</span></span>
* <span data-ttu-id="7d34c-168">Délimité</span><span class="sxs-lookup"><span data-stu-id="7d34c-168">Scoped</span></span>
* <span data-ttu-id="7d34c-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-169">Singleton</span></span>

<span data-ttu-id="7d34c-170">Les sections suivantes décrivent chacune des durées de vie précédentes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="7d34c-171">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="7d34c-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="7d34c-172">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-172">Transient</span></span>

<span data-ttu-id="7d34c-173">Des services à durée de vie temporaire (Transient) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="7d34c-174">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="7d34c-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="7d34c-175">Inscrire des services temporaires avec <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> .</span><span class="sxs-lookup"><span data-stu-id="7d34c-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="7d34c-176">Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="7d34c-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="7d34c-177">Délimité</span><span class="sxs-lookup"><span data-stu-id="7d34c-177">Scoped</span></span>

<span data-ttu-id="7d34c-178">Les services à durée de vie délimitée (Scoped) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="7d34c-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="7d34c-179">Inscrire les services délimités avec <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> .</span><span class="sxs-lookup"><span data-stu-id="7d34c-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="7d34c-180">Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="7d34c-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="7d34c-181">Lors de l’utilisation de Entity Framework Core, la <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> méthode d’extension inscrit les `DbContext` types avec une durée de vie limitée par défaut.</span><span class="sxs-lookup"><span data-stu-id="7d34c-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="7d34c-182">Ne résolvez ***pas*** un service étendu à partir d’un singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7d34c-183">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="7d34c-184">Il convient d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-184">It's fine to:</span></span>

* <span data-ttu-id="7d34c-185">Résoudre un service Singleton à partir d’un service délimité ou étendu.</span><span class="sxs-lookup"><span data-stu-id="7d34c-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="7d34c-186">Résoudre un service étendu à partir d’un autre service étendu ou temporaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="7d34c-187">Par défaut, dans l’environnement de développement, la résolution d’un service à partir d’un autre service avec une durée de vie plus longue lève une exception.</span><span class="sxs-lookup"><span data-stu-id="7d34c-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="7d34c-188">Pour plus d’informations, consultez [Validation de l’étendue](#sv).</span><span class="sxs-lookup"><span data-stu-id="7d34c-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="7d34c-189">Pour utiliser les services délimités dans des intergiciels (middleware), utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="7d34c-190">Injectez le service dans la méthode ou l’intergiciel (middleware) `Invoke` `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="7d34c-191">L’utilisation de l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) lève une exception Runtime, car elle force le service de portée à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="7d34c-192">L’exemple de la section [options de durée de vie et d’inscription](#lifetime-and-registration-options) illustre l' `InvokeAsync` approche.</span><span class="sxs-lookup"><span data-stu-id="7d34c-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="7d34c-193">Utilisez [un intergiciel (middleware) basé sur l’usine](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="7d34c-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="7d34c-194">Les intergiciels (middleware) inscrits à l’aide de cette approche sont activés par demande du client (connexion), ce qui permet d’injecter des services étendus dans la méthode de l’intergiciel (middleware) `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="7d34c-195">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="7d34c-196">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-196">Singleton</span></span>

<span data-ttu-id="7d34c-197">Les services de durée de vie Singleton sont créés :</span><span class="sxs-lookup"><span data-stu-id="7d34c-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="7d34c-198">La première fois qu’ils sont demandés.</span><span class="sxs-lookup"><span data-stu-id="7d34c-198">The first time they're requested.</span></span>
* <span data-ttu-id="7d34c-199">Par le développeur, lors de la fourniture d’une instance d’implémentation directement au conteneur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="7d34c-200">Cette approche est rarement nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-200">This approach is rarely needed.</span></span>

<span data-ttu-id="7d34c-201">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="7d34c-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="7d34c-202">Si l’application requiert un comportement Singleton, autorisez le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="7d34c-203">N’implémentez pas le modèle de conception Singleton et fournissez du code pour supprimer le singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="7d34c-204">Les services ne doivent jamais être supprimés par du code qui a résolu le service à partir du conteneur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="7d34c-205">Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprime automatiquement le singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="7d34c-206">Inscrire des services Singleton avec <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> .</span><span class="sxs-lookup"><span data-stu-id="7d34c-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="7d34c-207">Les services Singleton doivent être thread-safe et sont souvent utilisés dans les services sans État.</span><span class="sxs-lookup"><span data-stu-id="7d34c-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="7d34c-208">Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé lors de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="7d34c-209">Étant donné que la mémoire n’est pas libérée tant que l’application n’est pas arrêtée, envisagez d’utiliser la mémoire avec un service Singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="7d34c-210">Ne résolvez ***pas*** un service étendu à partir d’un singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7d34c-211">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="7d34c-212">Il est parfait de résoudre un service Singleton à partir d’un service étendu ou temporaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="7d34c-213">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="7d34c-213">Service registration methods</span></span>

<span data-ttu-id="7d34c-214">Le Framework fournit des méthodes d’extension d’inscription de service qui sont utiles dans des scénarios spécifiques :</span><span class="sxs-lookup"><span data-stu-id="7d34c-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="7d34c-215">Méthode</span><span class="sxs-lookup"><span data-stu-id="7d34c-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="7d34c-216">Automatique</span><span class="sxs-lookup"><span data-stu-id="7d34c-216">Automatic</span></span><br><span data-ttu-id="7d34c-217">object</span><span class="sxs-lookup"><span data-stu-id="7d34c-217">object</span></span><br><span data-ttu-id="7d34c-218">suppression</span><span class="sxs-lookup"><span data-stu-id="7d34c-218">disposal</span></span> | <span data-ttu-id="7d34c-219">Multiple</span><span class="sxs-lookup"><span data-stu-id="7d34c-219">Multiple</span></span><br><span data-ttu-id="7d34c-220">implémentations</span><span class="sxs-lookup"><span data-stu-id="7d34c-220">implementations</span></span> | <span data-ttu-id="7d34c-221">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="7d34c-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="7d34c-222">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d34c-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="7d34c-223">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-223">Yes</span></span>                             | <span data-ttu-id="7d34c-224">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-224">Yes</span></span>                         | <span data-ttu-id="7d34c-225">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="7d34c-226">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7d34c-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="7d34c-227">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-227">Yes</span></span>                             | <span data-ttu-id="7d34c-228">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-228">Yes</span></span>                         | <span data-ttu-id="7d34c-229">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="7d34c-230">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d34c-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="7d34c-231">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-231">Yes</span></span>                             | <span data-ttu-id="7d34c-232">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-232">No</span></span>                          | <span data-ttu-id="7d34c-233">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="7d34c-234">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7d34c-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="7d34c-235">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-235">No</span></span>                              | <span data-ttu-id="7d34c-236">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-236">Yes</span></span>                         | <span data-ttu-id="7d34c-237">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="7d34c-238">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7d34c-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="7d34c-239">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-239">No</span></span>                              | <span data-ttu-id="7d34c-240">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-240">No</span></span>                          | <span data-ttu-id="7d34c-241">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-241">Yes</span></span>       |

<span data-ttu-id="7d34c-242">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="7d34c-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="7d34c-243">Il est courant d’utiliser plusieurs implémentations lorsqu’il s’agit [de simuler des types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="7d34c-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="7d34c-244">Le Framework fournit également des `TryAdd{LIFETIME}` méthodes d’extension, qui inscrivent le service uniquement si aucune implémentation n’est déjà inscrite.</span><span class="sxs-lookup"><span data-stu-id="7d34c-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="7d34c-245">Dans l’exemple suivant, l’appel à `AddSingleton` s’inscrit `MyDependency` en tant qu’implémentation de `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="7d34c-246">L’appel à n' `TryAddSingleton` a aucun effet, car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="7d34c-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="7d34c-247">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="7d34c-248">Les méthodes [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) inscrivent le service uniquement s’il n’existe pas déjà une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="7d34c-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="7d34c-249">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="7d34c-250">Lors de l’inscription des services, le développeur doit ajouter une instance si l’un des mêmes types n’a pas déjà été ajouté.</span><span class="sxs-lookup"><span data-stu-id="7d34c-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="7d34c-251">En général, les auteurs de bibliothèque utilisent `TryAddEnumerable` pour éviter d’inscrire plusieurs copies d’une implémentation dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="7d34c-252">Dans l’exemple suivant, le premier appel à `TryAddEnumerable` s’inscrit `MyDependency` en tant qu’implémentation pour `IMyDependency1` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="7d34c-253">Le deuxième appel s’inscrit `MyDependency` pour `IMyDependency2` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="7d34c-254">Le troisième appel n’a aucun effet, car `IMyDependency1` a déjà une implémentation inscrite de `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="7d34c-255">L’inscription de service est généralement indépendante de l’ordre, sauf lors de l’inscription de plusieurs implémentations du même type.</span><span class="sxs-lookup"><span data-stu-id="7d34c-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="7d34c-256">`IServiceCollection` est une collection d' <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objets.</span><span class="sxs-lookup"><span data-stu-id="7d34c-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="7d34c-257">L’exemple suivant montre comment inscrire un service en créant et en ajoutant un `ServiceDescriptor` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="7d34c-258">Les `Add{LIFETIME}` méthodes intégrées utilisent la même approche.</span><span class="sxs-lookup"><span data-stu-id="7d34c-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="7d34c-259">Par exemple, consultez le [code source AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="7d34c-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="7d34c-260">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="7d34c-260">Constructor injection behavior</span></span>

<span data-ttu-id="7d34c-261">Les services peuvent être résolus à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="7d34c-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="7d34c-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="7d34c-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="7d34c-263">Crée des objets qui ne sont pas inscrits dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="7d34c-264">Utilisé avec les fonctionnalités d’infrastructure, telles que les [balises d’assistance](xref:mvc/views/tag-helpers/intro), les contrôleurs MVC et les [classeurs de modèles](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="7d34c-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="7d34c-265">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7d34c-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="7d34c-266">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="7d34c-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="7d34c-267">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="7d34c-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="7d34c-268">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="7d34c-269">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7d34c-269">Entity Framework contexts</span></span>

<span data-ttu-id="7d34c-270">Par défaut, Entity Framework contextes sont ajoutés au conteneur de service à l’aide de la [durée de vie limitée](#service-lifetimes) , car les opérations de base de données d’application Web sont normalement étendues à la demande du client.</span><span class="sxs-lookup"><span data-stu-id="7d34c-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="7d34c-271">Pour utiliser une durée de vie différente, spécifiez la durée de vie à l’aide d’une <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> surcharge.</span><span class="sxs-lookup"><span data-stu-id="7d34c-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="7d34c-272">Les services d’une durée de vie donnée ne doivent pas utiliser un contexte de base de données dont la durée de vie est inférieure à la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="7d34c-273">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="7d34c-273">Lifetime and registration options</span></span>

<span data-ttu-id="7d34c-274">Pour illustrer la différence entre les durées de vie de service et leurs options d’inscription, considérez les interfaces suivantes qui représentent une tâche comme une opération avec un identificateur, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="7d34c-275">Selon la configuration de la durée de vie du service d’une opération pour les interfaces suivantes, le conteneur fournit des instances identiques ou différentes du service lorsqu’il est demandé par une classe :</span><span class="sxs-lookup"><span data-stu-id="7d34c-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="7d34c-276">La `Operation` classe suivante implémente toutes les interfaces précédentes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="7d34c-277">Le `Operation` constructeur génère un GUID et stocke les 4 derniers caractères dans la `OperationId` propriété :</span><span class="sxs-lookup"><span data-stu-id="7d34c-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="7d34c-278">La `Startup.ConfigureServices` méthode crée plusieurs inscriptions de la `Operation` classe en fonction des durées de vie nommées :</span><span class="sxs-lookup"><span data-stu-id="7d34c-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="7d34c-279">L’exemple d’application montre les durées de vie des objets dans et entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="7d34c-280">Le `IndexModel` et l’intergiciel (middleware) demandent chaque genre de `IOperation` type et journalisent `OperationId` pour chacun d’entre eux :</span><span class="sxs-lookup"><span data-stu-id="7d34c-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="7d34c-281">À l’instar de `IndexModel` , l’intergiciel (middleware) résout les mêmes services :</span><span class="sxs-lookup"><span data-stu-id="7d34c-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="7d34c-282">Les services délimités doivent être résolus dans la `InvokeAsync` méthode :</span><span class="sxs-lookup"><span data-stu-id="7d34c-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="7d34c-283">La sortie de l’enregistreur d’événements affiche :</span><span class="sxs-lookup"><span data-stu-id="7d34c-283">The logger output shows:</span></span>

* <span data-ttu-id="7d34c-284">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="7d34c-284">*Transient* objects are always different.</span></span> <span data-ttu-id="7d34c-285">La `OperationId` valeur transitoire est différente dans `IndexModel` et dans l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="7d34c-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="7d34c-286">Les objets *délimités* sont les mêmes pour chaque demande, mais différents dans chaque demande.</span><span class="sxs-lookup"><span data-stu-id="7d34c-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="7d34c-287">Les objets *Singleton* sont les mêmes pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="7d34c-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="7d34c-288">Pour réduire la sortie de journalisation, définissez « journalisation : LogLevel : Microsoft : erreur » dans le *appsettings.Development.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="7d34c-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="7d34c-289">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="7d34c-289">Call services from main</span></span>

<span data-ttu-id="7d34c-290">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="7d34c-291">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="7d34c-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="7d34c-292">L’exemple suivant montre comment accéder au `IMyDependency` service étendu et appeler sa `WriteMessage` méthode dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="7d34c-293">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="7d34c-293">Scope validation</span></span>

<span data-ttu-id="7d34c-294">Lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments) et appelle [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) pour générer l’hôte, le fournisseur de services par défaut effectue des vérifications pour vérifier les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7d34c-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="7d34c-295">Les services délimités ne sont pas résolus à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="7d34c-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="7d34c-296">Les services délimités ne sont pas injectés dans les singletons.</span><span class="sxs-lookup"><span data-stu-id="7d34c-296">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="7d34c-297">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="7d34c-297">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="7d34c-298">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application lorsque le fournisseur démarre avec l’application et est supprimée lorsque l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7d34c-298">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="7d34c-299">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="7d34c-299">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="7d34c-300">Si un service étendu est créé dans le conteneur racine, la durée de vie du service est effectivement promue en Singleton, car il est supprimé uniquement par le conteneur racine lorsque l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7d34c-300">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="7d34c-301">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="7d34c-301">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="7d34c-302">Pour plus d’informations, consultez [Validation de l’étendue](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="7d34c-302">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="7d34c-303">Services de requête</span><span class="sxs-lookup"><span data-stu-id="7d34c-303">Request Services</span></span>

<span data-ttu-id="7d34c-304">Les services disponibles dans une demande de ASP.NET Core sont exposés via la collection [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="7d34c-304">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="7d34c-305">Lorsque des services sont demandés à l’intérieur d’une demande, les services et leurs dépendances sont résolus à partir de la `RequestServices` collection.</span><span class="sxs-lookup"><span data-stu-id="7d34c-305">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="7d34c-306">L’infrastructure crée une portée par demande et `RequestServices` expose le fournisseur de services étendus.</span><span class="sxs-lookup"><span data-stu-id="7d34c-306">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="7d34c-307">Tous les services délimités sont valides tant que la demande est active.</span><span class="sxs-lookup"><span data-stu-id="7d34c-307">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="7d34c-308">Préférez demander des dépendances en tant que paramètres de constructeur pour résoudre les services à partir de la `RequestServices` collection.</span><span class="sxs-lookup"><span data-stu-id="7d34c-308">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="7d34c-309">Il en résulte des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="7d34c-309">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="7d34c-310">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7d34c-310">Design services for dependency injection</span></span>

<span data-ttu-id="7d34c-311">Lors de la conception de services pour l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="7d34c-311">When designing services for dependency injection:</span></span>

* <span data-ttu-id="7d34c-312">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="7d34c-312">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="7d34c-313">Évitez de créer un état global en concevant des applications qui utilisent des services Singleton à la place.</span><span class="sxs-lookup"><span data-stu-id="7d34c-313">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="7d34c-314">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-314">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="7d34c-315">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="7d34c-315">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="7d34c-316">Rendez les services petits, bien factorisés et faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="7d34c-316">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="7d34c-317">Si une classe possède un grand nombre de dépendances injectées, il est possible qu’il s’agit d’un signe que la classe a trop de responsabilités et viole le [principe de responsabilité unique (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="7d34c-317">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="7d34c-318">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans de nouvelles classes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-318">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="7d34c-319">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-319">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="7d34c-320">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="7d34c-320">Disposal of services</span></span>

<span data-ttu-id="7d34c-321">Le conteneur appelle <xref:System.IDisposable.Dispose%2A> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-321">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="7d34c-322">Les services résolus à partir du conteneur ne doivent jamais être supprimés par le développeur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-322">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="7d34c-323">Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprime automatiquement le singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-323">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="7d34c-324">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="7d34c-324">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7d34c-325">La console de débogage affiche la sortie suivante après chaque actualisation de la page d’index :</span><span class="sxs-lookup"><span data-stu-id="7d34c-325">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="7d34c-326">Services non créés par le conteneur de service</span><span class="sxs-lookup"><span data-stu-id="7d34c-326">Services not created by the service container</span></span>

<span data-ttu-id="7d34c-327">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7d34c-327">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="7d34c-328">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="7d34c-328">In the preceding code:</span></span>

* <span data-ttu-id="7d34c-329">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-329">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="7d34c-330">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-330">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="7d34c-331">Le développeur est responsable de la suppression des services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-331">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="7d34c-332">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="7d34c-332">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="7d34c-333">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="7d34c-333">Transient, limited lifetime</span></span>

<span data-ttu-id="7d34c-334">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="7d34c-334">**Scenario**</span></span>

<span data-ttu-id="7d34c-335">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="7d34c-335">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="7d34c-336">L’instance est résolue dans l’étendue racine (conteneur racine).</span><span class="sxs-lookup"><span data-stu-id="7d34c-336">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="7d34c-337">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="7d34c-337">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="7d34c-338">**Solution**</span><span class="sxs-lookup"><span data-stu-id="7d34c-338">**Solution**</span></span>

<span data-ttu-id="7d34c-339">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="7d34c-339">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="7d34c-340">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="7d34c-340">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="7d34c-341">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="7d34c-341">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="7d34c-342">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-342">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="7d34c-343">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-343">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="7d34c-344">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="7d34c-344">Shared instance, limited lifetime</span></span>

<span data-ttu-id="7d34c-345">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="7d34c-345">**Scenario**</span></span>

<span data-ttu-id="7d34c-346">L’application requiert une <xref:System.IDisposable> instance partagée sur plusieurs services, mais l' <xref:System.IDisposable> instance doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-346">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="7d34c-347">**Solution**</span><span class="sxs-lookup"><span data-stu-id="7d34c-347">**Solution**</span></span>

<span data-ttu-id="7d34c-348">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-348">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="7d34c-349">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour créer un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="7d34c-349">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="7d34c-350">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="7d34c-350">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="7d34c-351">Supprimez l’étendue lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-351">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="7d34c-352">Instructions IDisposable générales</span><span class="sxs-lookup"><span data-stu-id="7d34c-352">General IDisposable guidelines</span></span>

* <span data-ttu-id="7d34c-353">N’inscrivez pas <xref:System.IDisposable> les instances avec une durée de vie transitoire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-353">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="7d34c-354">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="7d34c-354">Use the factory pattern instead.</span></span>
* <span data-ttu-id="7d34c-355">Ne résolvez pas <xref:System.IDisposable> les instances avec une durée de vie transitoire ou limitée dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="7d34c-355">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="7d34c-356">La seule exception à cela est que l’application crée/recrée et supprime <xref:System.IServiceProvider> , mais ce n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="7d34c-356">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="7d34c-357">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="7d34c-357">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="7d34c-358">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="7d34c-358">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="7d34c-359">Utilisez des étendues pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-359">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="7d34c-360">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="7d34c-360">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="7d34c-361">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="7d34c-361">Default service container replacement</span></span>

<span data-ttu-id="7d34c-362">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="7d34c-362">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="7d34c-363">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique qu’il ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="7d34c-363">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="7d34c-364">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="7d34c-364">Property injection</span></span>
* <span data-ttu-id="7d34c-365">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="7d34c-365">Injection based on name</span></span>
* <span data-ttu-id="7d34c-366">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="7d34c-366">Child containers</span></span>
* <span data-ttu-id="7d34c-367">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="7d34c-367">Custom lifetime management</span></span>
* <span data-ttu-id="7d34c-368">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="7d34c-368">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="7d34c-369">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="7d34c-369">Convention-based registration</span></span>

<span data-ttu-id="7d34c-370">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="7d34c-370">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="7d34c-371">Autofac</span><span class="sxs-lookup"><span data-stu-id="7d34c-371">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="7d34c-372">DryIoc</span><span class="sxs-lookup"><span data-stu-id="7d34c-372">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="7d34c-373">Grace</span><span class="sxs-lookup"><span data-stu-id="7d34c-373">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="7d34c-374">LightInject</span><span class="sxs-lookup"><span data-stu-id="7d34c-374">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="7d34c-375">Lamar</span><span class="sxs-lookup"><span data-stu-id="7d34c-375">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="7d34c-376">Stashbox</span><span class="sxs-lookup"><span data-stu-id="7d34c-376">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="7d34c-377">Unity</span><span class="sxs-lookup"><span data-stu-id="7d34c-377">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="7d34c-378">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="7d34c-378">Thread safety</span></span>

<span data-ttu-id="7d34c-379">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-379">Create thread-safe singleton services.</span></span> <span data-ttu-id="7d34c-380">Si un service Singleton a une dépendance vis-à-vis d’un service temporaire, le service temporaire peut également nécessiter la sécurité des threads en fonction de la façon dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-380">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="7d34c-381">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-381">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="7d34c-382">À l’instar d’un constructeur de type ( `static` ), il est garanti qu’il soit appelé une seule fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="7d34c-382">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="7d34c-383">Recommandations</span><span class="sxs-lookup"><span data-stu-id="7d34c-383">Recommendations</span></span>

* <span data-ttu-id="7d34c-384">`async/await` et la `Task` résolution de service basée sur n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7d34c-384">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="7d34c-385">Étant donné que C# ne prend pas en charge les constructeurs asynchrones, utilisez des méthodes asynchrones après la résolution synchrone du service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-385">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="7d34c-386">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-386">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="7d34c-387">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-387">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="7d34c-388">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="7d34c-388">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7d34c-389">De même, évitez les objets « garde-données » qui existent uniquement pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7d34c-389">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="7d34c-390">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="7d34c-390">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="7d34c-391">Évitez l’accès statique aux services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-391">Avoid static access to services.</span></span> <span data-ttu-id="7d34c-392">Par exemple, évitez de capturer [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) en tant que champ ou propriété statique à utiliser ailleurs.</span><span class="sxs-lookup"><span data-stu-id="7d34c-392">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="7d34c-393">Conservez les fabriques DI en mode rapide et synchrone.</span><span class="sxs-lookup"><span data-stu-id="7d34c-393">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="7d34c-394">Évitez d’utiliser le *modèle de localisation de service*.</span><span class="sxs-lookup"><span data-stu-id="7d34c-394">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="7d34c-395">Par exemple, n’appelez pas <xref:System.IServiceProvider.GetService%2A> pour obtenir une instance de service si vous pouvez utiliser l’injection de dépendance à la place :</span><span class="sxs-lookup"><span data-stu-id="7d34c-395">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="7d34c-396">**Incorrect :**</span><span class="sxs-lookup"><span data-stu-id="7d34c-396">**Incorrect:**</span></span>

    ![Code incorrect](dependency-injection/_static/bad.png)

  <span data-ttu-id="7d34c-398">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="7d34c-398">**Correct**:</span></span>

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
* <span data-ttu-id="7d34c-399">Une autre variante du localisateur de service à éviter est l’injection d’une fabrique qui résout les dépendances au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="7d34c-399">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="7d34c-400">Ces deux pratiques combinent des stratégies [Inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="7d34c-400">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="7d34c-401">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="7d34c-401">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="7d34c-402">Évitez les appels à <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-402">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="7d34c-403">L’appel de `BuildServiceProvider` se produit généralement lorsque le développeur souhaite résoudre un service dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-403">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="7d34c-404">Par exemple, considérez le cas où le `LoginPath` est chargé à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="7d34c-404">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="7d34c-405">Évitez l’approche suivante :</span><span class="sxs-lookup"><span data-stu-id="7d34c-405">Avoid the following approach:</span></span>

  ![code incorrect appelant BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="7d34c-407">Dans l’image précédente, la sélection de la ligne ondulée verte sous `services.BuildServiceProvider` affiche l’avertissement ASP0000 suivant :</span><span class="sxs-lookup"><span data-stu-id="7d34c-407">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="7d34c-408">ASP0000 l’appel de’BuildServiceProvider’à partir du code de l’application entraîne la création d’une copie supplémentaire des services Singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-408">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="7d34c-409">Envisagez des alternatives telles que la dépendance injectant des services en tant que paramètres pour « configurer ».</span><span class="sxs-lookup"><span data-stu-id="7d34c-409">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="7d34c-410">L’appel `BuildServiceProvider` de crée un deuxième conteneur, qui peut créer des singletons endommagés et provoquer des références aux graphiques d’objets sur plusieurs conteneurs.</span><span class="sxs-lookup"><span data-stu-id="7d34c-410">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="7d34c-411">Une bonne façon d’obtenir `LoginPath` est d’utiliser la prise en charge intégrée du modèle d’options pour di :</span><span class="sxs-lookup"><span data-stu-id="7d34c-411">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="7d34c-412">Les services transitoires jetables sont capturés par le conteneur pour la suppression.</span><span class="sxs-lookup"><span data-stu-id="7d34c-412">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="7d34c-413">Cela peut entraîner une fuite de mémoire si elle est résolue à partir du conteneur de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-413">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="7d34c-414">Activez la validation d’étendue pour vous assurer que l’application n’a pas de services délimités qui capturent des singletons.</span><span class="sxs-lookup"><span data-stu-id="7d34c-414">Enable scope validation to make sure the app doesn't have scoped services that capture singletons.</span></span> <span data-ttu-id="7d34c-415">Pour plus d’informations, consultez [Validation de l’étendue](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="7d34c-415">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="7d34c-416">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="7d34c-416">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="7d34c-417">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="7d34c-417">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="7d34c-418">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="7d34c-418">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="7d34c-419">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="7d34c-419">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="7d34c-420">Modèles recommandés pour l’architecture mutualisée dans DI</span><span class="sxs-lookup"><span data-stu-id="7d34c-420">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="7d34c-421">Le [noyau du verger](https://github.com/OrchardCMS/OrchardCore) est une infrastructure d’application permettant de créer des applications modulaires à plusieurs locataires sur ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7d34c-421">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="7d34c-422">Pour plus d’informations, consultez la documentation sur le [noyau du verger](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="7d34c-422">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="7d34c-423">Consultez les [exemples de noyaux de verger](https://github.com/OrchardCMS/OrchardCore.Samples) pour obtenir des exemples montrant comment créer des applications modulaires et mutualisées à l’aide de l’infrastructure principale du verger sans aucune de ses fonctionnalités propres à CMS.</span><span class="sxs-lookup"><span data-stu-id="7d34c-423">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7d34c-424">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="7d34c-424">Framework-provided services</span></span>

<span data-ttu-id="7d34c-425">La `Startup.ConfigureServices` méthode enregistre les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="7d34c-425">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="7d34c-426">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="7d34c-426">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="7d34c-427">Pour les applications basées sur les modèles de ASP.NET Core, le Framework inscrit plus de 250 services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-427">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="7d34c-428">Le tableau suivant répertorie un petit exemple de ces services inscrits au Framework :</span><span class="sxs-lookup"><span data-stu-id="7d34c-428">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="7d34c-429">Type de service</span><span class="sxs-lookup"><span data-stu-id="7d34c-429">Service Type</span></span>                                                                                    | <span data-ttu-id="7d34c-430">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="7d34c-430">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="7d34c-431">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-431">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="7d34c-432">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-432">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="7d34c-433">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="7d34c-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="7d34c-435">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-435">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="7d34c-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="7d34c-437">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-437">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="7d34c-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-438">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="7d34c-439">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="7d34c-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="7d34c-441">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="7d34c-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-442">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="7d34c-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="7d34c-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-444">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="7d34c-445">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7d34c-445">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="7d34c-446">Modèles de conférence norvégiens pour le développement d’applications DI</span><span class="sxs-lookup"><span data-stu-id="7d34c-446">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="7d34c-447">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d34c-447">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="7d34c-448">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="7d34c-448">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="7d34c-449">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="7d34c-449">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="7d34c-450">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="7d34c-450">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="7d34c-451">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d34c-451">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7d34c-452">Par [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="7d34c-452">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="7d34c-453">ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-453">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="7d34c-454">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-454">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="7d34c-455">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7d34c-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="7d34c-456">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7d34c-456">Overview of dependency injection</span></span>

<span data-ttu-id="7d34c-457">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7d34c-457">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="7d34c-458">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="7d34c-458">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="7d34c-459">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-459">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="7d34c-460">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-460">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="7d34c-461">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-461">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="7d34c-462">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-462">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="7d34c-463">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-463">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="7d34c-464">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-464">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="7d34c-465">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-465">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="7d34c-466">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-466">This implementation is difficult to unit test.</span></span> <span data-ttu-id="7d34c-467">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="7d34c-467">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="7d34c-468">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="7d34c-468">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="7d34c-469">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-469">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="7d34c-470">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-470">Registration of the dependency in a service container.</span></span> <span data-ttu-id="7d34c-471">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-471">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="7d34c-472">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-472">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="7d34c-473">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="7d34c-473">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="7d34c-474">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-474">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="7d34c-475">Dans l’[exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="7d34c-475">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="7d34c-476">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-476">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="7d34c-477">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-477">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="7d34c-478">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-478">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="7d34c-479">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-479">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="7d34c-480">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="7d34c-480">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="7d34c-481">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="7d34c-481">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="7d34c-482">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-482">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="7d34c-483">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-483">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7d34c-484">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7d34c-484">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="7d34c-485">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="7d34c-485">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="7d34c-486">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-486">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="7d34c-487">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="7d34c-487">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="7d34c-488">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="7d34c-488">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="7d34c-489">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-489">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="7d34c-490">Par exemple, `services.AddMvc()` ajoute les pages de services Razor et MVC requièrent.</span><span class="sxs-lookup"><span data-stu-id="7d34c-490">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="7d34c-491">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="7d34c-491">We recommended that apps follow this convention.</span></span> <span data-ttu-id="7d34c-492">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-492">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="7d34c-493">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="7d34c-493">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="7d34c-494">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="7d34c-494">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="7d34c-495">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-495">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="7d34c-496">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="7d34c-496">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="7d34c-497">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="7d34c-497">Services injected into Startup</span></span>

<span data-ttu-id="7d34c-498">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="7d34c-498">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="7d34c-499">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-499">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="7d34c-500">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-500">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="7d34c-501">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="7d34c-501">Framework-provided services</span></span>

<span data-ttu-id="7d34c-502">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="7d34c-502">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="7d34c-503">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="7d34c-503">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="7d34c-504">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7d34c-504">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="7d34c-505">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="7d34c-505">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="7d34c-506">Type de service</span><span class="sxs-lookup"><span data-stu-id="7d34c-506">Service Type</span></span> | <span data-ttu-id="7d34c-507">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="7d34c-507">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="7d34c-508">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-508">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="7d34c-509">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-509">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="7d34c-510">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="7d34c-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="7d34c-512">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="7d34c-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="7d34c-514">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-514">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="7d34c-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-515">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="7d34c-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="7d34c-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="7d34c-518">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="7d34c-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-519">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="7d34c-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="7d34c-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-521">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="7d34c-522">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="7d34c-522">Register additional services with extension methods</span></span>

<span data-ttu-id="7d34c-523">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-523">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="7d34c-524">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="7d34c-524">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="7d34c-525">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="7d34c-525">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="7d34c-526">Durées de service</span><span class="sxs-lookup"><span data-stu-id="7d34c-526">Service lifetimes</span></span>

<span data-ttu-id="7d34c-527">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="7d34c-527">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="7d34c-528">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-528">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="7d34c-529">Temporaire</span><span class="sxs-lookup"><span data-stu-id="7d34c-529">Transient</span></span>

<span data-ttu-id="7d34c-530">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-530">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="7d34c-531">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="7d34c-531">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="7d34c-532">Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="7d34c-532">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="7d34c-533">Délimité</span><span class="sxs-lookup"><span data-stu-id="7d34c-533">Scoped</span></span>

<span data-ttu-id="7d34c-534">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="7d34c-534">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="7d34c-535">Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="7d34c-535">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="7d34c-536">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-536">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="7d34c-537">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-537">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="7d34c-538">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-538">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="7d34c-539">Singleton</span><span class="sxs-lookup"><span data-stu-id="7d34c-539">Singleton</span></span>

<span data-ttu-id="7d34c-540">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="7d34c-540">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="7d34c-541">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="7d34c-541">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="7d34c-542">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-542">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="7d34c-543">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-543">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="7d34c-544">Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé au moment de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-544">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="7d34c-545">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-545">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="7d34c-546">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="7d34c-546">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="7d34c-547">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="7d34c-547">Service registration methods</span></span>

<span data-ttu-id="7d34c-548">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="7d34c-548">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="7d34c-549">Méthode</span><span class="sxs-lookup"><span data-stu-id="7d34c-549">Method</span></span> | <span data-ttu-id="7d34c-550">Automatique</span><span class="sxs-lookup"><span data-stu-id="7d34c-550">Automatic</span></span><br><span data-ttu-id="7d34c-551">object</span><span class="sxs-lookup"><span data-stu-id="7d34c-551">object</span></span><br><span data-ttu-id="7d34c-552">suppression</span><span class="sxs-lookup"><span data-stu-id="7d34c-552">disposal</span></span> | <span data-ttu-id="7d34c-553">Multiple</span><span class="sxs-lookup"><span data-stu-id="7d34c-553">Multiple</span></span><br><span data-ttu-id="7d34c-554">implémentations</span><span class="sxs-lookup"><span data-stu-id="7d34c-554">implementations</span></span> | <span data-ttu-id="7d34c-555">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="7d34c-555">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="7d34c-556">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d34c-556">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="7d34c-557">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-557">Yes</span></span> | <span data-ttu-id="7d34c-558">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-558">Yes</span></span> | <span data-ttu-id="7d34c-559">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-559">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="7d34c-560">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7d34c-560">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="7d34c-561">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-561">Yes</span></span> | <span data-ttu-id="7d34c-562">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-562">Yes</span></span> | <span data-ttu-id="7d34c-563">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-563">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="7d34c-564">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7d34c-564">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="7d34c-565">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-565">Yes</span></span> | <span data-ttu-id="7d34c-566">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-566">No</span></span> | <span data-ttu-id="7d34c-567">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-567">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="7d34c-568">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7d34c-568">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="7d34c-569">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-569">No</span></span> | <span data-ttu-id="7d34c-570">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-570">Yes</span></span> | <span data-ttu-id="7d34c-571">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-571">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="7d34c-572">Exemples :</span><span class="sxs-lookup"><span data-stu-id="7d34c-572">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="7d34c-573">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-573">No</span></span> | <span data-ttu-id="7d34c-574">Non</span><span class="sxs-lookup"><span data-stu-id="7d34c-574">No</span></span> | <span data-ttu-id="7d34c-575">Oui</span><span class="sxs-lookup"><span data-stu-id="7d34c-575">Yes</span></span> |

<span data-ttu-id="7d34c-576">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="7d34c-576">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="7d34c-577">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="7d34c-577">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="7d34c-578">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="7d34c-578">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="7d34c-579">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-579">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="7d34c-580">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="7d34c-580">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="7d34c-581">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-581">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="7d34c-582">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="7d34c-582">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="7d34c-583">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-583">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="7d34c-584">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-584">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="7d34c-585">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-585">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="7d34c-586">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-586">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="7d34c-587">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-587">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="7d34c-588">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-588">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="7d34c-589">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="7d34c-589">Constructor injection behavior</span></span>

<span data-ttu-id="7d34c-590">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-590">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="7d34c-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-591"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="7d34c-592">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="7d34c-592">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="7d34c-593">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="7d34c-593">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="7d34c-594">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="7d34c-594">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="7d34c-595">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="7d34c-595">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="7d34c-596">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-596">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="7d34c-597">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7d34c-597">Entity Framework contexts</span></span>

<span data-ttu-id="7d34c-598">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="7d34c-598">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="7d34c-599">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="7d34c-599">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="7d34c-600">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-600">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="7d34c-601">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="7d34c-601">Lifetime and registration options</span></span>

<span data-ttu-id="7d34c-602">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-602">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="7d34c-603">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="7d34c-603">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="7d34c-604">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-604">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="7d34c-605">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="7d34c-605">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="7d34c-606">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-606">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="7d34c-607">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="7d34c-607">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="7d34c-608">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-608">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="7d34c-609">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-609">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="7d34c-610">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-610">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="7d34c-611">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="7d34c-611">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="7d34c-612">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="7d34c-612">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="7d34c-613">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-613">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="7d34c-614">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="7d34c-614">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="7d34c-615">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-615">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="7d34c-616">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="7d34c-616">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="7d34c-617">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="7d34c-617">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="7d34c-618">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-618">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="7d34c-619">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="7d34c-619">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="7d34c-620">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-620">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="7d34c-621">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="7d34c-621">**First request:**</span></span>

<span data-ttu-id="7d34c-622">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7d34c-622">Controller operations:</span></span>

<span data-ttu-id="7d34c-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="7d34c-623">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="7d34c-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7d34c-624">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7d34c-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7d34c-625">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7d34c-626">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7d34c-626">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7d34c-627">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-627">`OperationService` operations:</span></span>

<span data-ttu-id="7d34c-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="7d34c-628">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="7d34c-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="7d34c-629">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="7d34c-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7d34c-630">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7d34c-631">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7d34c-631">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7d34c-632">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="7d34c-632">**Second request:**</span></span>

<span data-ttu-id="7d34c-633">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="7d34c-633">Controller operations:</span></span>

<span data-ttu-id="7d34c-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="7d34c-634">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="7d34c-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7d34c-635">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7d34c-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7d34c-636">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7d34c-637">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7d34c-637">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7d34c-638">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-638">`OperationService` operations:</span></span>

<span data-ttu-id="7d34c-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="7d34c-639">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="7d34c-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="7d34c-640">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="7d34c-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="7d34c-641">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="7d34c-642">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="7d34c-642">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="7d34c-643">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="7d34c-643">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="7d34c-644">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="7d34c-644">*Transient* objects are always different.</span></span> <span data-ttu-id="7d34c-645">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="7d34c-645">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="7d34c-646">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="7d34c-646">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="7d34c-647">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="7d34c-647">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="7d34c-648">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7d34c-648">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="7d34c-649">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="7d34c-649">Call services from main</span></span>

<span data-ttu-id="7d34c-650">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-650">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="7d34c-651">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="7d34c-651">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="7d34c-652">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="7d34c-652">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="7d34c-653">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="7d34c-653">Scope validation</span></span>

<span data-ttu-id="7d34c-654">Quand l’application s’exécute dans l’environnement de développement, le fournisseur de services par défaut effectue des contrôles pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="7d34c-654">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="7d34c-655">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="7d34c-655">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="7d34c-656">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="7d34c-656">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="7d34c-657">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="7d34c-657">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="7d34c-658">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7d34c-658">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="7d34c-659">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="7d34c-659">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="7d34c-660">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="7d34c-660">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="7d34c-661">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="7d34c-661">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="7d34c-662">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="7d34c-662">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="7d34c-663">Services de requête</span><span class="sxs-lookup"><span data-stu-id="7d34c-663">Request Services</span></span>

<span data-ttu-id="7d34c-664">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="7d34c-664">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="7d34c-665">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="7d34c-665">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="7d34c-666">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-666">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="7d34c-667">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="7d34c-667">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="7d34c-668">Demandez plutôt les types nécessaires à la classe via des constructeurs de classe et autorisez le framework à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-668">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="7d34c-669">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="7d34c-669">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="7d34c-670">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="7d34c-670">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="7d34c-671">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="7d34c-671">Design services for dependency injection</span></span>

<span data-ttu-id="7d34c-672">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="7d34c-672">Best practices are to:</span></span>

* <span data-ttu-id="7d34c-673">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-673">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="7d34c-674">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="7d34c-674">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="7d34c-675">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="7d34c-675">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="7d34c-676">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-676">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="7d34c-677">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="7d34c-677">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="7d34c-678">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="7d34c-678">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="7d34c-679">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="7d34c-679">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="7d34c-680">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-680">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="7d34c-681">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-681">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="7d34c-682">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="7d34c-682">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="7d34c-683">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="7d34c-683">Disposal of services</span></span>

<span data-ttu-id="7d34c-684">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-684">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="7d34c-685">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7d34c-685">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="7d34c-686">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="7d34c-686">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="7d34c-687">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7d34c-687">In the following example:</span></span>

* <span data-ttu-id="7d34c-688">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="7d34c-688">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="7d34c-689">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="7d34c-689">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="7d34c-690">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-690">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="7d34c-691">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="7d34c-691">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="7d34c-692">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="7d34c-692">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="7d34c-693">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="7d34c-693">Transient, limited lifetime</span></span>

<span data-ttu-id="7d34c-694">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="7d34c-694">**Scenario**</span></span>

<span data-ttu-id="7d34c-695">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="7d34c-695">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="7d34c-696">L’instance est résolue dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="7d34c-696">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="7d34c-697">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="7d34c-697">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="7d34c-698">**Solution**</span><span class="sxs-lookup"><span data-stu-id="7d34c-698">**Solution**</span></span>

<span data-ttu-id="7d34c-699">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="7d34c-699">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="7d34c-700">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="7d34c-700">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="7d34c-701">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="7d34c-701">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="7d34c-702">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="7d34c-702">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="7d34c-703">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="7d34c-703">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="7d34c-704">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="7d34c-704">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="7d34c-705">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="7d34c-705">**Scenario**</span></span>

<span data-ttu-id="7d34c-706">L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-706">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="7d34c-707">**Solution**</span><span class="sxs-lookup"><span data-stu-id="7d34c-707">**Solution**</span></span>

<span data-ttu-id="7d34c-708">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="7d34c-708">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="7d34c-709">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="7d34c-709">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="7d34c-710">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="7d34c-710">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="7d34c-711">Supprimez l’étendue lorsque la durée de vie doit se terminer.</span><span class="sxs-lookup"><span data-stu-id="7d34c-711">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="7d34c-712">Instructions générales</span><span class="sxs-lookup"><span data-stu-id="7d34c-712">General Guidelines</span></span>

* <span data-ttu-id="7d34c-713">N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire.</span><span class="sxs-lookup"><span data-stu-id="7d34c-713">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="7d34c-714">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="7d34c-714">Use the factory pattern instead.</span></span>
* <span data-ttu-id="7d34c-715">Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="7d34c-715">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="7d34c-716">La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="7d34c-716">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="7d34c-717">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="7d34c-717">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="7d34c-718">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="7d34c-718">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="7d34c-719">Les portées doivent être utilisées pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-719">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="7d34c-720">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="7d34c-720">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="7d34c-721">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="7d34c-721">Default service container replacement</span></span>

<span data-ttu-id="7d34c-722">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="7d34c-722">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="7d34c-723">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="7d34c-723">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="7d34c-724">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="7d34c-724">Property injection</span></span>
* <span data-ttu-id="7d34c-725">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="7d34c-725">Injection based on name</span></span>
* <span data-ttu-id="7d34c-726">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="7d34c-726">Child containers</span></span>
* <span data-ttu-id="7d34c-727">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="7d34c-727">Custom lifetime management</span></span>
* <span data-ttu-id="7d34c-728">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="7d34c-728">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="7d34c-729">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="7d34c-729">Convention-based registration</span></span>

<span data-ttu-id="7d34c-730">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="7d34c-730">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="7d34c-731">Autofac</span><span class="sxs-lookup"><span data-stu-id="7d34c-731">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="7d34c-732">DryIoc</span><span class="sxs-lookup"><span data-stu-id="7d34c-732">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="7d34c-733">Grace</span><span class="sxs-lookup"><span data-stu-id="7d34c-733">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="7d34c-734">LightInject</span><span class="sxs-lookup"><span data-stu-id="7d34c-734">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="7d34c-735">Lamar</span><span class="sxs-lookup"><span data-stu-id="7d34c-735">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="7d34c-736">Stashbox</span><span class="sxs-lookup"><span data-stu-id="7d34c-736">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="7d34c-737">Unity</span><span class="sxs-lookup"><span data-stu-id="7d34c-737">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="7d34c-738">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="7d34c-738">Thread safety</span></span>

<span data-ttu-id="7d34c-739">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-739">Create thread-safe singleton services.</span></span> <span data-ttu-id="7d34c-740">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="7d34c-740">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="7d34c-741">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="7d34c-741">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="7d34c-742">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="7d34c-742">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="7d34c-743">Recommandations</span><span class="sxs-lookup"><span data-stu-id="7d34c-743">Recommendations</span></span>

* <span data-ttu-id="7d34c-744">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7d34c-744">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="7d34c-745">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="7d34c-745">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="7d34c-746">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-746">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="7d34c-747">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-747">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="7d34c-748">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="7d34c-748">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="7d34c-749">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="7d34c-749">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="7d34c-750">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="7d34c-750">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="7d34c-751">Évitez l’accès statique aux services.</span><span class="sxs-lookup"><span data-stu-id="7d34c-751">Avoid static access to services.</span></span> <span data-ttu-id="7d34c-752">Par exemple, évitez de taper statiquement [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pour une utilisation ailleurs.</span><span class="sxs-lookup"><span data-stu-id="7d34c-752">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="7d34c-753">Évitez d’utiliser le *modèle de localisation de service*, qui combine les stratégies [d’inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="7d34c-753">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="7d34c-754">N’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service lorsque vous pouvez utiliser l’injection de code à la place :</span><span class="sxs-lookup"><span data-stu-id="7d34c-754">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="7d34c-755">**Incorrect :**</span><span class="sxs-lookup"><span data-stu-id="7d34c-755">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="7d34c-756">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="7d34c-756">**Correct**:</span></span>

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

* <span data-ttu-id="7d34c-757">Évitez d’injecter une fabrique qui résout les dépendances au moment de l’exécution à l’aide de <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="7d34c-757">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="7d34c-758">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="7d34c-758">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="7d34c-759">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="7d34c-759">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="7d34c-760">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="7d34c-760">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="7d34c-761">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="7d34c-761">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="7d34c-762">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="7d34c-762">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d34c-763">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7d34c-763">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="7d34c-764">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d34c-764">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="7d34c-765">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="7d34c-765">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="7d34c-766">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="7d34c-766">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="7d34c-767">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="7d34c-767">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="7d34c-768">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d34c-768">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
