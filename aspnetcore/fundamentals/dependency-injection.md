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
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628792"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="fc143-103">Injection de dépendances dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc143-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc143-104">Par [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="fc143-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="fc143-105">ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fc143-106">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fc143-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fc143-107">Pour plus d’informations sur l’injection de dépendances d’options, consultez <xref:fundamentals/configuration/options> .</span><span class="sxs-lookup"><span data-stu-id="fc143-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="fc143-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc143-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fc143-109">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="fc143-109">Overview of dependency injection</span></span>

<span data-ttu-id="fc143-110">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="fc143-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="fc143-111">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="fc143-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="fc143-112">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="fc143-113">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="fc143-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="fc143-114">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fc143-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="fc143-115">Les dépendances de code, telles que l’exemple précédent, sont problématiques et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fc143-116">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="fc143-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="fc143-117">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="fc143-118">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fc143-119">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="fc143-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fc143-120">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="fc143-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fc143-121">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="fc143-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fc143-122">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fc143-123">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fc143-124">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc143-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fc143-125">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fc143-126">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="fc143-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fc143-127">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fc143-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fc143-128">Dans l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="fc143-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="fc143-129">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="fc143-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="fc143-130">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fc143-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fc143-131">La <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> méthode enregistre le service avec une durée de vie limitée, la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="fc143-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="fc143-132">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="fc143-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="fc143-133">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="fc143-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="fc143-134">Avec le modèle DI :</span><span class="sxs-lookup"><span data-stu-id="fc143-134">With the DI pattern:</span></span>

* <span data-ttu-id="fc143-135">Le contrôleur n’utilise pas le type concret `MyDependency` , mais uniquement l’interface `IMyDependency` .</span><span class="sxs-lookup"><span data-stu-id="fc143-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="fc143-136">Cela facilite la modification de l’implémentation que le contrôleur utilise sans modifier le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="fc143-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="fc143-137">Le contrôleur ne crée pas d’instance de `MyDependency` , il est créé par le conteneur di.</span><span class="sxs-lookup"><span data-stu-id="fc143-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="fc143-138">L’implémentation de l' `IMyDependency` interface peut être améliorée à l’aide de l’API de journalisation intégrée :</span><span class="sxs-lookup"><span data-stu-id="fc143-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="fc143-139">La méthode mise à jour `ConfigureServices` inscrit la nouvelle `IMyDependency` implémentation :</span><span class="sxs-lookup"><span data-stu-id="fc143-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="fc143-140">`MyDependency2` demande un <xref:Microsoft.Extensions.Logging.ILogger`1> dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="fc143-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="fc143-141">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="fc143-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fc143-142">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fc143-143">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="fc143-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fc143-144">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="fc143-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="fc143-145">`ILogger<TCategoryName>` est un [service fourni par le Framework](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="fc143-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="fc143-146">Le conteneur se résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui évite d’avoir à inscrire chaque [type construit (Générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span><span class="sxs-lookup"><span data-stu-id="fc143-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="fc143-147">Dans la terminologie d’injection de dépendances, un service :</span><span class="sxs-lookup"><span data-stu-id="fc143-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="fc143-148">Est généralement un objet qui fournit un service à un autre code de l’application, tel que le `IMyDependency` service.</span><span class="sxs-lookup"><span data-stu-id="fc143-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="fc143-149">N’est pas associé à un service Web, bien que le service puisse utiliser un service Web.</span><span class="sxs-lookup"><span data-stu-id="fc143-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="fc143-150">Le Framework fournit un système de [journalisation](xref:fundamentals/logging/index) robuste.</span><span class="sxs-lookup"><span data-stu-id="fc143-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="fc143-151">Les `IMyDependency` implémentations ont été écrites pour illustrer la di de base, et non l’implémentation de la journalisation.</span><span class="sxs-lookup"><span data-stu-id="fc143-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="fc143-152">La plupart des applications n’ont pas besoin d’écrire des enregistreurs.</span><span class="sxs-lookup"><span data-stu-id="fc143-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="fc143-153">Le code suivant illustre l’utilisation de la journalisation par défaut, qui ne nécessite pas l’inscription de services dans `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fc143-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="fc143-154">À l’aide du code précédent, il n’est pas nécessaire de mettre à jour, `ConfigureServices` car la [journalisation](xref:fundamentals/logging/index) est fournie par le Framework :</span><span class="sxs-lookup"><span data-stu-id="fc143-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="fc143-155">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="fc143-155">Services injected into Startup</span></span>

<span data-ttu-id="fc143-156">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="fc143-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="fc143-157">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="fc143-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="fc143-158">Pour plus d’informations, consultez <xref:fundamentals/startup> et [configuration de l’accès au démarrage](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="fc143-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="fc143-159">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="fc143-159">Register additional services with extension methods</span></span>

<span data-ttu-id="fc143-160">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service :</span><span class="sxs-lookup"><span data-stu-id="fc143-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="fc143-161">La Convention consiste à utiliser une seule `Add{SERVICE_NAME}` méthode d’extension pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="fc143-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="fc143-162">Les services dépendants sont également inscrits.</span><span class="sxs-lookup"><span data-stu-id="fc143-162">The dependent services are also registered.</span></span>

<span data-ttu-id="fc143-163">Le code suivant est généré par le Razor modèle pages à l’aide de comptes d’utilisateur individuels et montre comment ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :</span><span class="sxs-lookup"><span data-stu-id="fc143-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="fc143-164">Pour plus d’informations, consultez <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> et <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="fc143-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="fc143-165">Consultez la section [combinaison](#csc) de la collection de services pour obtenir des instructions sur l’écriture d’une méthode d’extension pour inscrire des services.</span><span class="sxs-lookup"><span data-stu-id="fc143-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="fc143-166">Durées de service</span><span class="sxs-lookup"><span data-stu-id="fc143-166">Service lifetimes</span></span>

<span data-ttu-id="fc143-167">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="fc143-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="fc143-168">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="fc143-169">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-169">Transient</span></span>

<span data-ttu-id="fc143-170">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="fc143-171">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="fc143-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="fc143-172">Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="fc143-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="fc143-173">Délimité</span><span class="sxs-lookup"><span data-stu-id="fc143-173">Scoped</span></span>

<span data-ttu-id="fc143-174">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="fc143-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="fc143-175">Lors de l’utilisation de Entity Framework Core, la <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> méthode d’extension inscrit les `DbContext` types avec une durée de vie limitée par défaut.</span><span class="sxs-lookup"><span data-stu-id="fc143-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="fc143-176">Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="fc143-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="fc143-177">Utilisez les services délimités dans l’intergiciel (middleware) avec l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="fc143-178">Injectez le service dans `Invoke` la `InvokeAsync` méthode ou.</span><span class="sxs-lookup"><span data-stu-id="fc143-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fc143-179">L’injection par [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) lève une exception au moment de l’exécution, car elle force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="fc143-180">L’exemple dans les [options de durée de vie et d’inscription](#lifetime-and-registration-options) utilise l' `InvokeAsync` approche.</span><span class="sxs-lookup"><span data-stu-id="fc143-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="fc143-181">[Intergiciel (middleware) basé sur l’usine](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="fc143-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="fc143-182">Les méthodes d’extension de <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> vérifient si le type inscrit d’un middleware implémente <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="fc143-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="fc143-183">Si c’est le cas, l’instance de <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> inscrite dans le conteneur est utilisée pour résoudre l’implémentation de <xref:Microsoft.AspNetCore.Http.IMiddleware>, au lieu de la logique d’activation de middleware basée sur une convention.</span><span class="sxs-lookup"><span data-stu-id="fc143-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="fc143-184">Le middleware est inscrit comme service délimité ou temporaire dans le conteneur de service de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="fc143-185">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="fc143-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="fc143-186">Ne résolvez ***pas*** un service étendu à partir d’un singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="fc143-187">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="fc143-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="fc143-188">Il convient d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-188">It's fine to:</span></span>

* <span data-ttu-id="fc143-189">Résoudre un service Singleton à partir d’un service délimité ou étendu.</span><span class="sxs-lookup"><span data-stu-id="fc143-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="fc143-190">Résoudre un service étendu à partir d’un autre service étendu ou temporaire.</span><span class="sxs-lookup"><span data-stu-id="fc143-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="fc143-191">Par défaut, dans l’environnement de développement, la résolution d’un service à partir d’un autre service avec une durée de vie plus longue lève une exception.</span><span class="sxs-lookup"><span data-stu-id="fc143-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="fc143-192">Pour plus d’informations, consultez [Validation de l’étendue](#sv).</span><span class="sxs-lookup"><span data-stu-id="fc143-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="fc143-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-193">Singleton</span></span>

<span data-ttu-id="fc143-194">Les services de durée de vie Singleton ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) sont créés :</span><span class="sxs-lookup"><span data-stu-id="fc143-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="fc143-195">La première fois qu’ils sont demandés.</span><span class="sxs-lookup"><span data-stu-id="fc143-195">The first time they're requested.</span></span>
* <span data-ttu-id="fc143-196">Par le développeur, lors de la fourniture d’une instance d’implémentation directement au conteneur.</span><span class="sxs-lookup"><span data-stu-id="fc143-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="fc143-197">Cette approche est rarement nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fc143-197">This approach is rarely needed.</span></span>

<span data-ttu-id="fc143-198">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="fc143-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="fc143-199">Si l’application requiert un comportement Singleton, autorisez le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="fc143-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="fc143-200">N’implémentez pas le modèle de conception Singleton et fournissez du code pour supprimer le singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="fc143-201">Les services ne doivent jamais être supprimés par du code qui a résolu le service à partir d’un conteneur.</span><span class="sxs-lookup"><span data-stu-id="fc143-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="fc143-202">Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprimera automatiquement le singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="fc143-203">Les services Singleton doivent être thread-safe et sont souvent utilisés dans les services sans État.</span><span class="sxs-lookup"><span data-stu-id="fc143-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="fc143-204">Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé lors de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="fc143-205">Étant donné que la mémoire n’est pas libérée tant que l’application n’est pas arrêtée, l’utilisation de la mémoire avec un singleton doit être prise en compte.</span><span class="sxs-lookup"><span data-stu-id="fc143-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="fc143-206">Ne résolvez ***pas*** un service étendu à partir d’un singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="fc143-207">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="fc143-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="fc143-208">Il est parfait de résoudre un service Singleton à partir d’un service étendu ou temporaire.</span><span class="sxs-lookup"><span data-stu-id="fc143-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="fc143-209">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="fc143-209">Service registration methods</span></span>

<span data-ttu-id="fc143-210">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="fc143-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="fc143-211">Méthode</span><span class="sxs-lookup"><span data-stu-id="fc143-211">Method</span></span> | <span data-ttu-id="fc143-212">Automatique</span><span class="sxs-lookup"><span data-stu-id="fc143-212">Automatic</span></span><br><span data-ttu-id="fc143-213">object</span><span class="sxs-lookup"><span data-stu-id="fc143-213">object</span></span><br><span data-ttu-id="fc143-214">suppression</span><span class="sxs-lookup"><span data-stu-id="fc143-214">disposal</span></span> | <span data-ttu-id="fc143-215">Multiple</span><span class="sxs-lookup"><span data-stu-id="fc143-215">Multiple</span></span><br><span data-ttu-id="fc143-216">implémentations</span><span class="sxs-lookup"><span data-stu-id="fc143-216">implementations</span></span> | <span data-ttu-id="fc143-217">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="fc143-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="fc143-218">Exemple :</span><span class="sxs-lookup"><span data-stu-id="fc143-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="fc143-219">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-219">Yes</span></span> | <span data-ttu-id="fc143-220">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-220">Yes</span></span> | <span data-ttu-id="fc143-221">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="fc143-222">Exemples :</span><span class="sxs-lookup"><span data-stu-id="fc143-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="fc143-223">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-223">Yes</span></span> | <span data-ttu-id="fc143-224">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-224">Yes</span></span> | <span data-ttu-id="fc143-225">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="fc143-226">Exemple :</span><span class="sxs-lookup"><span data-stu-id="fc143-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="fc143-227">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-227">Yes</span></span> | <span data-ttu-id="fc143-228">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-228">No</span></span> | <span data-ttu-id="fc143-229">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="fc143-230">Exemples :</span><span class="sxs-lookup"><span data-stu-id="fc143-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="fc143-231">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-231">No</span></span> | <span data-ttu-id="fc143-232">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-232">Yes</span></span> | <span data-ttu-id="fc143-233">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="fc143-234">Exemples :</span><span class="sxs-lookup"><span data-stu-id="fc143-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="fc143-235">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-235">No</span></span> | <span data-ttu-id="fc143-236">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-236">No</span></span> | <span data-ttu-id="fc143-237">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-237">Yes</span></span> |

<span data-ttu-id="fc143-238">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="fc143-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="fc143-239">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="fc143-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="fc143-240">`TryAdd{LIFETIME}` les méthodes inscrivent le service si aucune implémentation n’est déjà inscrite.</span><span class="sxs-lookup"><span data-stu-id="fc143-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="fc143-241">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fc143-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="fc143-242">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="fc143-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="fc143-243">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="fc143-244">Les méthodes [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent le service s’il n’existe pas déjà une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="fc143-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="fc143-245">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="fc143-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="fc143-246">Lors de l’inscription des services, le développeur doit ajouter une instance si l’un des mêmes types n’a pas déjà été ajouté.</span><span class="sxs-lookup"><span data-stu-id="fc143-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="fc143-247">En général, les auteurs de bibliothèque utilisent `TryAddEnumerable` pour éviter d’inscrire plusieurs copies d’une implémentation dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="fc143-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="fc143-248">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="fc143-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="fc143-249">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="fc143-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="fc143-250">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="fc143-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="fc143-251">L’inscription de service est généralement indépendante de l’ordre, sauf lors de l’inscription de plusieurs implémentations du même type.</span><span class="sxs-lookup"><span data-stu-id="fc143-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="fc143-252">`IServiceCollection` est une collection de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="fc143-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="fc143-253">Le code suivant montre comment ajouter un service à l’aide d’un constructeur :</span><span class="sxs-lookup"><span data-stu-id="fc143-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="fc143-254">Les `Add{LIFETIME}` méthodes utilisent la même approche.</span><span class="sxs-lookup"><span data-stu-id="fc143-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="fc143-255">Par exemple, consultez le [code source pour AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="fc143-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="fc143-256">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="fc143-256">Constructor injection behavior</span></span>

<span data-ttu-id="fc143-257">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="fc143-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="fc143-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="fc143-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="fc143-259">Crée des objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="fc143-260">Utilisé avec les fonctionnalités d’infrastructure, telles que les [balises d’assistance](xref:mvc/views/tag-helpers/intro), les contrôleurs MVC et les [classeurs de modèles](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="fc143-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="fc143-261">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="fc143-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="fc143-262">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="fc143-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="fc143-263">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="fc143-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="fc143-264">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fc143-265">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="fc143-265">Entity Framework contexts</span></span>

<span data-ttu-id="fc143-266">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="fc143-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="fc143-267">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="fc143-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="fc143-268">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="fc143-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fc143-269">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="fc143-269">Lifetime and registration options</span></span>

<span data-ttu-id="fc143-270">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches comme une opération avec un identificateur, `OperationId` .</span><span class="sxs-lookup"><span data-stu-id="fc143-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="fc143-271">Selon la configuration de la durée de vie du service d’une opération pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’elle est demandée par une classe :</span><span class="sxs-lookup"><span data-stu-id="fc143-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="fc143-272">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fc143-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="fc143-273">Le `Operation` constructeur génère les 4 derniers caractères d’un GUID si aucun n’est fourni :</span><span class="sxs-lookup"><span data-stu-id="fc143-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="fc143-274">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="fc143-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="fc143-275">L’exemple d’application montre les durées de vie des objets dans et entre les demandes.</span><span class="sxs-lookup"><span data-stu-id="fc143-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="fc143-276">L’exemple d’application `IndexModel` et l’intergiciel (middleware) demandent chaque `IOperation` type de type et journalisent les `OperationId` éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="fc143-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="fc143-277">L’intergiciel est semblable à `IndexModel` et résout les mêmes services :</span><span class="sxs-lookup"><span data-stu-id="fc143-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="fc143-278">Les services délimités doivent être résolus dans la `InvokeAsync` méthode :</span><span class="sxs-lookup"><span data-stu-id="fc143-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="fc143-279">La sortie de l’enregistreur d’événements affiche :</span><span class="sxs-lookup"><span data-stu-id="fc143-279">The logger output shows:</span></span>

* <span data-ttu-id="fc143-280">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="fc143-280">*Transient* objects are always different.</span></span> <span data-ttu-id="fc143-281">La `OperationId` valeur transitoire est différente dans le `IndexModel` et l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="fc143-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="fc143-282">Les objets *délimités* sont les mêmes dans chaque demande, mais différents dans chaque demande.</span><span class="sxs-lookup"><span data-stu-id="fc143-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="fc143-283">Les objets *Singleton* sont les mêmes pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="fc143-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="fc143-284">Pour réduire la sortie de journalisation, définissez « journalisation : LogLevel : Microsoft : erreur » dans le *appsettings.Development.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="fc143-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="fc143-285">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="fc143-285">Call services from main</span></span>

<span data-ttu-id="fc143-286">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fc143-287">Cette approche est utile pour accéder à un service étendu au démarrage pour exécuter des tâches d’initialisation :</span><span class="sxs-lookup"><span data-stu-id="fc143-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="fc143-288">Le code précédent consiste à [Ajouter l’initialiseur de valeur initiale](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) dans le didacticiel sur les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="fc143-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="fc143-289">L’exemple suivant montre comment obtenir un contexte pour `IMyDependency` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="fc143-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="fc143-290">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="fc143-290">Scope validation</span></span>

<span data-ttu-id="fc143-291">Lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments) et appelle [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) pour générer l’hôte, le fournisseur de services par défaut effectue des vérifications pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="fc143-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="fc143-292">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="fc143-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="fc143-293">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="fc143-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="fc143-294">Les services temporaires ne sont pas injectés directement ou indirectement dans des singletons ou des services délimités.</span><span class="sxs-lookup"><span data-stu-id="fc143-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="fc143-295">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="fc143-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="fc143-296">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application lorsque le fournisseur démarre avec l’application et est supprimée lorsque l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fc143-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="fc143-297">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="fc143-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="fc143-298">Si un service étendu est créé dans le conteneur racine, la durée de vie du service est effectivement promue en Singleton, car il est supprimé uniquement par le conteneur racine lorsque l’application est arrêtée.</span><span class="sxs-lookup"><span data-stu-id="fc143-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="fc143-299">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="fc143-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="fc143-300">Pour plus d’informations, consultez [Validation de l’étendue](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="fc143-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="fc143-301">Services de requête</span><span class="sxs-lookup"><span data-stu-id="fc143-301">Request Services</span></span>

<span data-ttu-id="fc143-302">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="fc143-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="fc143-303">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="fc143-304">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="fc143-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="fc143-305">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="fc143-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="fc143-306">Au lieu de cela, demandez les types que les classes requièrent via les constructeurs de classe et autorisez l’infrastructure à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="fc143-307">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="fc143-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="fc143-308">ASP.NET Core crée une étendue par demande et `RequestServices` expose le fournisseur de services étendus.</span><span class="sxs-lookup"><span data-stu-id="fc143-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="fc143-309">Tous les services délimités sont valides tant que la demande est active.</span><span class="sxs-lookup"><span data-stu-id="fc143-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="fc143-310">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="fc143-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fc143-311">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="fc143-311">Design services for dependency injection</span></span>

<span data-ttu-id="fc143-312">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="fc143-312">Best practices are to:</span></span>

* <span data-ttu-id="fc143-313">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="fc143-314">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="fc143-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="fc143-315">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="fc143-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="fc143-316">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="fc143-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fc143-317">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="fc143-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fc143-318">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="fc143-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fc143-319">Si une classe semble avoir trop de dépendances injectées, il s’agit généralement d’un signe que la classe a un trop grand nombre de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="fc143-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fc143-320">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="fc143-321">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fc143-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fc143-322">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="fc143-322">Disposal of services</span></span>

<span data-ttu-id="fc143-323">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="fc143-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="fc143-324">Les services ne doivent jamais être supprimés par un code qui a résolu le service à partir d’un conteneur.</span><span class="sxs-lookup"><span data-stu-id="fc143-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="fc143-325">Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprime le singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="fc143-326">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="fc143-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="fc143-327">La console de débogage affiche la sortie suivante après chaque actualisation de la page d’index :</span><span class="sxs-lookup"><span data-stu-id="fc143-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="fc143-328">Services non créés par le conteneur de service</span><span class="sxs-lookup"><span data-stu-id="fc143-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="fc143-329">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fc143-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="fc143-330">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="fc143-330">In the preceding code:</span></span>

* <span data-ttu-id="fc143-331">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="fc143-332">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="fc143-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="fc143-333">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="fc143-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="fc143-334">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fc143-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="fc143-335">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="fc143-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="fc143-336">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="fc143-336">Transient, limited lifetime</span></span>

<span data-ttu-id="fc143-337">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="fc143-337">**Scenario**</span></span>

<span data-ttu-id="fc143-338">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="fc143-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="fc143-339">L’instance est résolue dans l’étendue racine (conteneur racine).</span><span class="sxs-lookup"><span data-stu-id="fc143-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="fc143-340">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="fc143-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="fc143-341">**Solution**</span><span class="sxs-lookup"><span data-stu-id="fc143-341">**Solution**</span></span>

<span data-ttu-id="fc143-342">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="fc143-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="fc143-343">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="fc143-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="fc143-344">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="fc143-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="fc143-345">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="fc143-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="fc143-346">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="fc143-347">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="fc143-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="fc143-348">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="fc143-348">**Scenario**</span></span>

<span data-ttu-id="fc143-349">L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="fc143-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="fc143-350">**Solution**</span><span class="sxs-lookup"><span data-stu-id="fc143-350">**Solution**</span></span>

<span data-ttu-id="fc143-351">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="fc143-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="fc143-352">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="fc143-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="fc143-353">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="fc143-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="fc143-354">Supprimez l’étendue lorsque la durée de vie doit se terminer.</span><span class="sxs-lookup"><span data-stu-id="fc143-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="fc143-355">Instructions IDisposable générales</span><span class="sxs-lookup"><span data-stu-id="fc143-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="fc143-356">N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire.</span><span class="sxs-lookup"><span data-stu-id="fc143-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="fc143-357">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="fc143-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="fc143-358">Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="fc143-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="fc143-359">La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="fc143-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="fc143-360">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="fc143-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="fc143-361">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="fc143-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="fc143-362">Les portées doivent être utilisées pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="fc143-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="fc143-363">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="fc143-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="fc143-364">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="fc143-364">Default service container replacement</span></span>

<span data-ttu-id="fc143-365">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="fc143-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="fc143-366">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="fc143-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="fc143-367">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="fc143-367">Property injection</span></span>
* <span data-ttu-id="fc143-368">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="fc143-368">Injection based on name</span></span>
* <span data-ttu-id="fc143-369">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="fc143-369">Child containers</span></span>
* <span data-ttu-id="fc143-370">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="fc143-370">Custom lifetime management</span></span>
* <span data-ttu-id="fc143-371">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="fc143-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="fc143-372">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="fc143-372">Convention-based registration</span></span>

<span data-ttu-id="fc143-373">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="fc143-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="fc143-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="fc143-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="fc143-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="fc143-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="fc143-376">Grace</span><span class="sxs-lookup"><span data-stu-id="fc143-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="fc143-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="fc143-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="fc143-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="fc143-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="fc143-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="fc143-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="fc143-380">Unity</span><span class="sxs-lookup"><span data-stu-id="fc143-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="fc143-381">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="fc143-381">Thread safety</span></span>

<span data-ttu-id="fc143-382">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="fc143-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="fc143-383">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="fc143-384">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="fc143-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="fc143-385">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="fc143-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="fc143-386">Recommandations</span><span class="sxs-lookup"><span data-stu-id="fc143-386">Recommendations</span></span>

* <span data-ttu-id="fc143-387">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fc143-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="fc143-388">C# ne prend pas en charge les constructeurs asynchrones.</span><span class="sxs-lookup"><span data-stu-id="fc143-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="fc143-389">Le modèle recommandé consiste à utiliser des méthodes asynchrones après la résolution synchrone du service.</span><span class="sxs-lookup"><span data-stu-id="fc143-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="fc143-390">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="fc143-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="fc143-391">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="fc143-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="fc143-392">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="fc143-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="fc143-393">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="fc143-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="fc143-394">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="fc143-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="fc143-395">Évitez l’accès statique aux services.</span><span class="sxs-lookup"><span data-stu-id="fc143-395">Avoid static access to services.</span></span> <span data-ttu-id="fc143-396">Par exemple, évitez de taper statiquement [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pour une utilisation ailleurs).</span><span class="sxs-lookup"><span data-stu-id="fc143-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="fc143-397">Conservez les fabriques DI en mode rapide et synchrone.</span><span class="sxs-lookup"><span data-stu-id="fc143-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="fc143-398">Évitez d’utiliser le *modèle de localisation de service*.</span><span class="sxs-lookup"><span data-stu-id="fc143-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="fc143-399">Par exemple, n’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service si vous pouvez utiliser l’injection de dépendance à la place :</span><span class="sxs-lookup"><span data-stu-id="fc143-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="fc143-400">**Incorrect :**</span><span class="sxs-lookup"><span data-stu-id="fc143-400">**Incorrect:**</span></span>

    ![Code incorrect](dependency-injection/_static/bad.png)

  <span data-ttu-id="fc143-402">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="fc143-402">**Correct**:</span></span>

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
* <span data-ttu-id="fc143-403">Une autre variante du localisateur de service à éviter est l’injection d’une fabrique qui résout les dépendances au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="fc143-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="fc143-404">Ces deux pratiques combinent des stratégies [Inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="fc143-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="fc143-405">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="fc143-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="fc143-406">Évitez les appels à <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fc143-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="fc143-407">L’appel de `BuildServiceProvider` se produit généralement lorsque le développeur souhaite résoudre un service dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fc143-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="fc143-408">Par exemple, considérez le cas où vous devez obtenir le `LoginPath` de la configuration de.</span><span class="sxs-lookup"><span data-stu-id="fc143-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="fc143-409">Évitez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fc143-409">Avoid the following code:</span></span>

  ![code incorrect appelant BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="fc143-411">Dans l’image précédente, la sélection de la ligne ondulée verte sous `services.BuildServiceProvider` affiche l’avertissement ASP0000 suivant :</span><span class="sxs-lookup"><span data-stu-id="fc143-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="fc143-412">ASP0000 l’appel de’BuildServiceProvider’à partir du code de l’application entraîne la création d’une copie supplémentaire des services Singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="fc143-413">Envisagez des alternatives telles que la dépendance injectant des services en tant que paramètres pour « configurer ».</span><span class="sxs-lookup"><span data-stu-id="fc143-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="fc143-414">L’appel `BuildServiceProvider` de crée un deuxième conteneur, qui peut créer des singletons endommagés et provoquer des références aux graphiques d’objets sur plusieurs conteneurs.</span><span class="sxs-lookup"><span data-stu-id="fc143-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="fc143-415">Une méthode correcte pour obtenir `LoginPath` est d’utiliser le modèle d’option avec di :</span><span class="sxs-lookup"><span data-stu-id="fc143-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="fc143-416">Les services transitoires jetables sont capturés par le conteneur pour la suppression.</span><span class="sxs-lookup"><span data-stu-id="fc143-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="fc143-417">Cela peut entraîner une fuite de mémoire si elle est résolue à partir du conteneur de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="fc143-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="fc143-418">Activez la validation d’étendue pour vous assurer que l’application n’a pas de services délimités capturant les singletons.</span><span class="sxs-lookup"><span data-stu-id="fc143-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="fc143-419">Pour plus d’informations, consultez [Validation de l’étendue](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="fc143-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="fc143-420">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="fc143-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fc143-421">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="fc143-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fc143-422">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="fc143-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fc143-423">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="fc143-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="fc143-424">Modèles recommandés pour l’architecture mutualisée dans DI</span><span class="sxs-lookup"><span data-stu-id="fc143-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="fc143-425">Le [cœur du verger](https://github.com/OrchardCMS/OrchardCore) fournit une architecture mutualisée.</span><span class="sxs-lookup"><span data-stu-id="fc143-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="fc143-426">Pour plus d’informations, consultez la documentation sur le [noyau du verger](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="fc143-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="fc143-427">Consultez les exemples d’applications à l’adresse https://github.com/OrchardCMS/OrchardCore.Samples pour obtenir des exemples de création d’applications modulaires et mutualisées à l’aide de l’infrastructure principale du verger uniquement sans les fonctionnalités spécifiques au CMS.</span><span class="sxs-lookup"><span data-stu-id="fc143-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fc143-428">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="fc143-428">Framework-provided services</span></span>

<span data-ttu-id="fc143-429">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="fc143-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fc143-430">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="fc143-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="fc143-431">Les applications basées sur un ASP.NET Core modèles ont plus de 250 services inscrits par le Framework.</span><span class="sxs-lookup"><span data-stu-id="fc143-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="fc143-432">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="fc143-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="fc143-433">Type de service</span><span class="sxs-lookup"><span data-stu-id="fc143-433">Service Type</span></span> | <span data-ttu-id="fc143-434">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="fc143-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fc143-435">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="fc143-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="fc143-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="fc143-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="fc143-439">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="fc143-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="fc143-441">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="fc143-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="fc143-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="fc143-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="fc143-445">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="fc143-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="fc143-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="fc143-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="fc143-449">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="fc143-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="fc143-450">Modèles de conférence norvégiens pour le développement d’applications DI</span><span class="sxs-lookup"><span data-stu-id="fc143-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fc143-451">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc143-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="fc143-452">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="fc143-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="fc143-453">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="fc143-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="fc143-454">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="fc143-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="fc143-455">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc143-455">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fc143-456">Par [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="fc143-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="fc143-457">ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fc143-458">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fc143-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fc143-459">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc143-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fc143-460">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="fc143-460">Overview of dependency injection</span></span>

<span data-ttu-id="fc143-461">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="fc143-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="fc143-462">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="fc143-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="fc143-463">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="fc143-464">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="fc143-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="fc143-465">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fc143-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="fc143-466">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fc143-467">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="fc143-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="fc143-468">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="fc143-469">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fc143-470">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="fc143-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fc143-471">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="fc143-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fc143-472">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="fc143-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fc143-473">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fc143-474">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fc143-475">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc143-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fc143-476">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fc143-477">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="fc143-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fc143-478">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fc143-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fc143-479">Dans l’[exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="fc143-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="fc143-480">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="fc143-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="fc143-481">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="fc143-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="fc143-482">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="fc143-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fc143-483">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fc143-484">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="fc143-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fc143-485">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="fc143-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="fc143-486">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="fc143-487">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fc143-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fc143-488">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="fc143-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="fc143-489">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="fc143-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="fc143-490">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fc143-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fc143-491">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="fc143-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="fc143-492">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="fc143-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="fc143-493">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="fc143-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="fc143-494">Par exemple, `services.AddMvc()` ajoute les pages de services Razor et MVC requièrent.</span><span class="sxs-lookup"><span data-stu-id="fc143-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="fc143-495">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="fc143-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="fc143-496">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="fc143-497">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="fc143-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="fc143-498">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="fc143-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="fc143-499">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="fc143-500">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="fc143-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="fc143-501">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="fc143-501">Services injected into Startup</span></span>

<span data-ttu-id="fc143-502">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="fc143-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="fc143-503">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="fc143-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="fc143-504">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fc143-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fc143-505">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="fc143-505">Framework-provided services</span></span>

<span data-ttu-id="fc143-506">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="fc143-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fc143-507">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="fc143-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="fc143-508">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="fc143-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="fc143-509">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="fc143-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="fc143-510">Type de service</span><span class="sxs-lookup"><span data-stu-id="fc143-510">Service Type</span></span> | <span data-ttu-id="fc143-511">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="fc143-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fc143-512">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="fc143-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="fc143-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="fc143-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="fc143-516">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="fc143-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="fc143-518">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="fc143-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="fc143-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="fc143-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="fc143-522">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="fc143-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="fc143-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="fc143-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="fc143-526">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="fc143-526">Register additional services with extension methods</span></span>

<span data-ttu-id="fc143-527">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="fc143-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="fc143-528">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="fc143-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="fc143-529">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="fc143-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="fc143-530">Durées de service</span><span class="sxs-lookup"><span data-stu-id="fc143-530">Service lifetimes</span></span>

<span data-ttu-id="fc143-531">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="fc143-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="fc143-532">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="fc143-533">Temporaire</span><span class="sxs-lookup"><span data-stu-id="fc143-533">Transient</span></span>

<span data-ttu-id="fc143-534">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="fc143-535">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="fc143-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="fc143-536">Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="fc143-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="fc143-537">Délimité</span><span class="sxs-lookup"><span data-stu-id="fc143-537">Scoped</span></span>

<span data-ttu-id="fc143-538">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="fc143-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="fc143-539">Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="fc143-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="fc143-540">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="fc143-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fc143-541">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="fc143-542">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="fc143-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="fc143-543">Singleton</span><span class="sxs-lookup"><span data-stu-id="fc143-543">Singleton</span></span>

<span data-ttu-id="fc143-544">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="fc143-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="fc143-545">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="fc143-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="fc143-546">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="fc143-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="fc143-547">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="fc143-548">Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé au moment de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="fc143-549">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="fc143-550">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="fc143-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="fc143-551">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="fc143-551">Service registration methods</span></span>

<span data-ttu-id="fc143-552">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="fc143-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="fc143-553">Méthode</span><span class="sxs-lookup"><span data-stu-id="fc143-553">Method</span></span> | <span data-ttu-id="fc143-554">Automatique</span><span class="sxs-lookup"><span data-stu-id="fc143-554">Automatic</span></span><br><span data-ttu-id="fc143-555">object</span><span class="sxs-lookup"><span data-stu-id="fc143-555">object</span></span><br><span data-ttu-id="fc143-556">suppression</span><span class="sxs-lookup"><span data-stu-id="fc143-556">disposal</span></span> | <span data-ttu-id="fc143-557">Multiple</span><span class="sxs-lookup"><span data-stu-id="fc143-557">Multiple</span></span><br><span data-ttu-id="fc143-558">implémentations</span><span class="sxs-lookup"><span data-stu-id="fc143-558">implementations</span></span> | <span data-ttu-id="fc143-559">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="fc143-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="fc143-560">Exemple :</span><span class="sxs-lookup"><span data-stu-id="fc143-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="fc143-561">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-561">Yes</span></span> | <span data-ttu-id="fc143-562">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-562">Yes</span></span> | <span data-ttu-id="fc143-563">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="fc143-564">Exemples :</span><span class="sxs-lookup"><span data-stu-id="fc143-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="fc143-565">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-565">Yes</span></span> | <span data-ttu-id="fc143-566">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-566">Yes</span></span> | <span data-ttu-id="fc143-567">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="fc143-568">Exemple :</span><span class="sxs-lookup"><span data-stu-id="fc143-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="fc143-569">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-569">Yes</span></span> | <span data-ttu-id="fc143-570">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-570">No</span></span> | <span data-ttu-id="fc143-571">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="fc143-572">Exemples :</span><span class="sxs-lookup"><span data-stu-id="fc143-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="fc143-573">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-573">No</span></span> | <span data-ttu-id="fc143-574">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-574">Yes</span></span> | <span data-ttu-id="fc143-575">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="fc143-576">Exemples :</span><span class="sxs-lookup"><span data-stu-id="fc143-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="fc143-577">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-577">No</span></span> | <span data-ttu-id="fc143-578">Non</span><span class="sxs-lookup"><span data-stu-id="fc143-578">No</span></span> | <span data-ttu-id="fc143-579">Oui</span><span class="sxs-lookup"><span data-stu-id="fc143-579">Yes</span></span> |

<span data-ttu-id="fc143-580">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="fc143-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="fc143-581">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="fc143-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="fc143-582">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="fc143-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="fc143-583">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fc143-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="fc143-584">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="fc143-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="fc143-585">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fc143-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="fc143-586">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="fc143-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="fc143-587">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="fc143-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="fc143-588">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="fc143-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="fc143-589">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="fc143-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="fc143-590">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="fc143-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="fc143-591">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="fc143-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="fc143-592">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="fc143-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="fc143-593">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="fc143-593">Constructor injection behavior</span></span>

<span data-ttu-id="fc143-594">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="fc143-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="fc143-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="fc143-596">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="fc143-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="fc143-597">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="fc143-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="fc143-598">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="fc143-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="fc143-599">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="fc143-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="fc143-600">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fc143-601">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="fc143-601">Entity Framework contexts</span></span>

<span data-ttu-id="fc143-602">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="fc143-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="fc143-603">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="fc143-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="fc143-604">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="fc143-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fc143-605">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="fc143-605">Lifetime and registration options</span></span>

<span data-ttu-id="fc143-606">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fc143-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="fc143-607">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="fc143-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="fc143-608">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fc143-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="fc143-609">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="fc143-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="fc143-610">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fc143-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="fc143-611">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="fc143-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="fc143-612">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fc143-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="fc143-613">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="fc143-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="fc143-614">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fc143-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="fc143-615">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="fc143-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="fc143-616">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="fc143-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="fc143-617">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="fc143-618">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="fc143-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="fc143-619">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="fc143-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="fc143-620">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="fc143-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="fc143-621">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="fc143-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="fc143-622">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fc143-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="fc143-623">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="fc143-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="fc143-624">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="fc143-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="fc143-625">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="fc143-625">**First request:**</span></span>

<span data-ttu-id="fc143-626">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="fc143-626">Controller operations:</span></span>

<span data-ttu-id="fc143-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="fc143-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="fc143-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fc143-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fc143-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc143-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc143-630">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc143-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc143-631">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="fc143-631">`OperationService` operations:</span></span>

<span data-ttu-id="fc143-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="fc143-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="fc143-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fc143-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fc143-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc143-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc143-635">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc143-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc143-636">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="fc143-636">**Second request:**</span></span>

<span data-ttu-id="fc143-637">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="fc143-637">Controller operations:</span></span>

<span data-ttu-id="fc143-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="fc143-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="fc143-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fc143-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fc143-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc143-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc143-641">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc143-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc143-642">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="fc143-642">`OperationService` operations:</span></span>

<span data-ttu-id="fc143-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="fc143-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="fc143-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fc143-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fc143-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fc143-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fc143-646">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fc143-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fc143-647">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="fc143-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="fc143-648">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="fc143-648">*Transient* objects are always different.</span></span> <span data-ttu-id="fc143-649">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="fc143-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="fc143-650">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="fc143-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="fc143-651">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="fc143-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="fc143-652">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fc143-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="fc143-653">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="fc143-653">Call services from main</span></span>

<span data-ttu-id="fc143-654">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fc143-655">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="fc143-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="fc143-656">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="fc143-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="fc143-657">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="fc143-657">Scope validation</span></span>

<span data-ttu-id="fc143-658">Quand l’application s’exécute dans l’environnement de développement, le fournisseur de services par défaut effectue des contrôles pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="fc143-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="fc143-659">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="fc143-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="fc143-660">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="fc143-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="fc143-661">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="fc143-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="fc143-662">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fc143-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="fc143-663">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="fc143-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="fc143-664">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="fc143-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="fc143-665">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="fc143-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="fc143-666">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="fc143-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="fc143-667">Services de requête</span><span class="sxs-lookup"><span data-stu-id="fc143-667">Request Services</span></span>

<span data-ttu-id="fc143-668">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="fc143-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="fc143-669">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="fc143-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="fc143-670">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="fc143-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="fc143-671">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="fc143-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="fc143-672">Demandez plutôt les types nécessaires à la classe via des constructeurs de classe et autorisez le framework à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="fc143-673">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="fc143-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="fc143-674">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="fc143-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fc143-675">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="fc143-675">Design services for dependency injection</span></span>

<span data-ttu-id="fc143-676">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="fc143-676">Best practices are to:</span></span>

* <span data-ttu-id="fc143-677">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="fc143-678">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="fc143-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="fc143-679">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="fc143-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="fc143-680">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="fc143-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fc143-681">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="fc143-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fc143-682">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="fc143-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fc143-683">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="fc143-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fc143-684">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="fc143-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="fc143-685">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fc143-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="fc143-686">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="fc143-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fc143-687">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="fc143-687">Disposal of services</span></span>

<span data-ttu-id="fc143-688">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="fc143-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="fc143-689">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="fc143-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="fc143-690">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="fc143-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="fc143-691">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fc143-691">In the following example:</span></span>

* <span data-ttu-id="fc143-692">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fc143-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="fc143-693">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="fc143-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="fc143-694">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="fc143-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="fc143-695">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fc143-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="fc143-696">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="fc143-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="fc143-697">Transitoire, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="fc143-697">Transient, limited lifetime</span></span>

<span data-ttu-id="fc143-698">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="fc143-698">**Scenario**</span></span>

<span data-ttu-id="fc143-699">L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="fc143-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="fc143-700">L’instance est résolue dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="fc143-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="fc143-701">L’instance doit être supprimée avant la fin de l’étendue.</span><span class="sxs-lookup"><span data-stu-id="fc143-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="fc143-702">**Solution**</span><span class="sxs-lookup"><span data-stu-id="fc143-702">**Solution**</span></span>

<span data-ttu-id="fc143-703">Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente.</span><span class="sxs-lookup"><span data-stu-id="fc143-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="fc143-704">Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final.</span><span class="sxs-lookup"><span data-stu-id="fc143-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="fc143-705">Si le type final a d’autres dépendances, la fabrique peut :</span><span class="sxs-lookup"><span data-stu-id="fc143-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="fc143-706">Recevoir un <xref:System.IServiceProvider> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="fc143-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="fc143-707">Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.</span><span class="sxs-lookup"><span data-stu-id="fc143-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="fc143-708">Instance partagée, durée de vie limitée</span><span class="sxs-lookup"><span data-stu-id="fc143-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="fc143-709">**Scénario**</span><span class="sxs-lookup"><span data-stu-id="fc143-709">**Scenario**</span></span>

<span data-ttu-id="fc143-710">L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="fc143-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="fc143-711">**Solution**</span><span class="sxs-lookup"><span data-stu-id="fc143-711">**Solution**</span></span>

<span data-ttu-id="fc143-712">Inscrivez l’instance avec une durée de vie limitée.</span><span class="sxs-lookup"><span data-stu-id="fc143-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="fc143-713">Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> .</span><span class="sxs-lookup"><span data-stu-id="fc143-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="fc143-714">Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis.</span><span class="sxs-lookup"><span data-stu-id="fc143-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="fc143-715">Supprimez l’étendue lorsque la durée de vie doit se terminer.</span><span class="sxs-lookup"><span data-stu-id="fc143-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="fc143-716">Instructions générales</span><span class="sxs-lookup"><span data-stu-id="fc143-716">General Guidelines</span></span>

* <span data-ttu-id="fc143-717">N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire.</span><span class="sxs-lookup"><span data-stu-id="fc143-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="fc143-718">Utilisez à la place le modèle de fabrique.</span><span class="sxs-lookup"><span data-stu-id="fc143-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="fc143-719">Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine.</span><span class="sxs-lookup"><span data-stu-id="fc143-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="fc143-720">La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.</span><span class="sxs-lookup"><span data-stu-id="fc143-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="fc143-721">La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même.</span><span class="sxs-lookup"><span data-stu-id="fc143-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="fc143-722">Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.</span><span class="sxs-lookup"><span data-stu-id="fc143-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="fc143-723">Les portées doivent être utilisées pour contrôler les durées de vie des services.</span><span class="sxs-lookup"><span data-stu-id="fc143-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="fc143-724">Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.</span><span class="sxs-lookup"><span data-stu-id="fc143-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="fc143-725">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="fc143-725">Default service container replacement</span></span>

<span data-ttu-id="fc143-726">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="fc143-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="fc143-727">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="fc143-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="fc143-728">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="fc143-728">Property injection</span></span>
* <span data-ttu-id="fc143-729">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="fc143-729">Injection based on name</span></span>
* <span data-ttu-id="fc143-730">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="fc143-730">Child containers</span></span>
* <span data-ttu-id="fc143-731">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="fc143-731">Custom lifetime management</span></span>
* <span data-ttu-id="fc143-732">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="fc143-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="fc143-733">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="fc143-733">Convention-based registration</span></span>

<span data-ttu-id="fc143-734">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="fc143-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="fc143-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="fc143-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="fc143-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="fc143-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="fc143-737">Grace</span><span class="sxs-lookup"><span data-stu-id="fc143-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="fc143-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="fc143-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="fc143-739">Lamar</span><span class="sxs-lookup"><span data-stu-id="fc143-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="fc143-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="fc143-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="fc143-741">Unity</span><span class="sxs-lookup"><span data-stu-id="fc143-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="fc143-742">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="fc143-742">Thread safety</span></span>

<span data-ttu-id="fc143-743">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="fc143-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="fc143-744">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="fc143-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="fc143-745">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="fc143-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="fc143-746">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="fc143-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="fc143-747">Recommandations</span><span class="sxs-lookup"><span data-stu-id="fc143-747">Recommendations</span></span>

* <span data-ttu-id="fc143-748">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="fc143-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="fc143-749">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="fc143-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="fc143-750">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="fc143-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="fc143-751">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="fc143-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="fc143-752">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="fc143-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="fc143-753">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="fc143-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="fc143-754">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="fc143-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="fc143-755">Évitez l’accès statique aux services.</span><span class="sxs-lookup"><span data-stu-id="fc143-755">Avoid static access to services.</span></span> <span data-ttu-id="fc143-756">Par exemple, évitez de taper statiquement [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pour une utilisation ailleurs.</span><span class="sxs-lookup"><span data-stu-id="fc143-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="fc143-757">Évitez d’utiliser le *modèle de localisation de service*, qui combine les stratégies [d’inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="fc143-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="fc143-758">N’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service lorsque vous pouvez utiliser l’injection de code à la place :</span><span class="sxs-lookup"><span data-stu-id="fc143-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="fc143-759">**Incorrect :**</span><span class="sxs-lookup"><span data-stu-id="fc143-759">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="fc143-760">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="fc143-760">**Correct**:</span></span>

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

* <span data-ttu-id="fc143-761">Évitez d’injecter une fabrique qui résout les dépendances au moment de l’exécution à l’aide de <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="fc143-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="fc143-762">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="fc143-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="fc143-763">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="fc143-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fc143-764">Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.</span><span class="sxs-lookup"><span data-stu-id="fc143-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fc143-765">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="fc143-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fc143-766">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="fc143-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc143-767">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="fc143-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fc143-768">Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc143-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="fc143-769">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="fc143-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="fc143-770">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="fc143-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="fc143-771">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="fc143-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="fc143-772">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc143-772">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
