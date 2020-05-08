---
title: ASP.NET Core Blazor l’injection de dépendances
author: guardrex
description: Découvrez comment Blazor les applications peuvent injecter des services dans des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: e96698bd0bd8f3f3b290ba24bc8169efb16f1d03
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967530"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="e2785-103">ASP.NET Core l’injection de dépendances éblouissantes</span><span class="sxs-lookup"><span data-stu-id="e2785-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="e2785-104">Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="e2785-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e2785-105">Éblouissant prend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2785-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2785-106">Les applications peuvent utiliser des services intégrés en les injectant dans des composants.</span><span class="sxs-lookup"><span data-stu-id="e2785-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="e2785-107">Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.</span><span class="sxs-lookup"><span data-stu-id="e2785-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="e2785-108">La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central.</span><span class="sxs-lookup"><span data-stu-id="e2785-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="e2785-109">Cela peut être utile dans les applications éblouissantes pour :</span><span class="sxs-lookup"><span data-stu-id="e2785-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="e2785-110">Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .</span><span class="sxs-lookup"><span data-stu-id="e2785-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="e2785-111">Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence.</span><span class="sxs-lookup"><span data-stu-id="e2785-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="e2785-112">Par exemple, considérez une `IDataAccess` interface pour accéder aux données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="e2785-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="e2785-113">L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="e2785-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="e2785-114">Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret.</span><span class="sxs-lookup"><span data-stu-id="e2785-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="e2785-115">L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="e2785-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="e2785-116">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="e2785-116">Default services</span></span>

<span data-ttu-id="e2785-117">Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="e2785-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="e2785-118">Service</span><span class="sxs-lookup"><span data-stu-id="e2785-118">Service</span></span> | <span data-ttu-id="e2785-119">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="e2785-119">Lifetime</span></span> | <span data-ttu-id="e2785-120">Description</span><span class="sxs-lookup"><span data-stu-id="e2785-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="e2785-121">Temporaire</span><span class="sxs-lookup"><span data-stu-id="e2785-121">Transient</span></span> | <span data-ttu-id="e2785-122">Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.</span><span class="sxs-lookup"><span data-stu-id="e2785-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="e2785-123">L’instance de `HttpClient` dans une application de webassembly éblouissant utilise le navigateur pour gérer le trafic HTTP en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="e2785-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="e2785-124">Les applications serveur éblouissantes n’incluent `HttpClient` pas une configuration en tant que service par défaut.</span><span class="sxs-lookup"><span data-stu-id="e2785-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="e2785-125">Fournissez `HttpClient` un à une application de serveur éblouissante.</span><span class="sxs-lookup"><span data-stu-id="e2785-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="e2785-126">Pour plus d'informations, consultez <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="e2785-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="e2785-127">Singleton (webassembly éblouissant)</span><span class="sxs-lookup"><span data-stu-id="e2785-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="e2785-128">Étendu (serveur éblouissant)</span><span class="sxs-lookup"><span data-stu-id="e2785-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="e2785-129">Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués.</span><span class="sxs-lookup"><span data-stu-id="e2785-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="e2785-130">Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="e2785-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="e2785-131">Singleton (webassembly éblouissant)</span><span class="sxs-lookup"><span data-stu-id="e2785-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="e2785-132">Étendu (serveur éblouissant)</span><span class="sxs-lookup"><span data-stu-id="e2785-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="e2785-133">Contient des assistances pour l’utilisation des URI et de l’état de navigation.</span><span class="sxs-lookup"><span data-stu-id="e2785-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="e2785-134">Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="e2785-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="e2785-135">Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="e2785-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="e2785-136">Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="e2785-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="e2785-137">Ajouter des services à une application</span><span class="sxs-lookup"><span data-stu-id="e2785-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="e2785-138">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="e2785-138">Blazor WebAssembly</span></span>

<span data-ttu-id="e2785-139">Configurez les services de la collection de services `Main` de l’application dans la méthode de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2785-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="e2785-140">Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite `IMyDependency`pour :</span><span class="sxs-lookup"><span data-stu-id="e2785-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="e2785-141">Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="e2785-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="e2785-142">Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :</span><span class="sxs-lookup"><span data-stu-id="e2785-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="e2785-143">L’hôte fournit également une instance de configuration centrale pour l’application.</span><span class="sxs-lookup"><span data-stu-id="e2785-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="e2785-144">En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, `InitializeWeatherAsync` *appSettings. JSON*) à :</span><span class="sxs-lookup"><span data-stu-id="e2785-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="e2785-145">Serveur Blazor</span><span class="sxs-lookup"><span data-stu-id="e2785-145">Blazor Server</span></span>

<span data-ttu-id="e2785-146">Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="e2785-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="e2785-147">Une `ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, qui est une liste d’objets descripteurs de service (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>), est passée à la méthode.</span><span class="sxs-lookup"><span data-stu-id="e2785-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="e2785-148">Les services sont ajoutés en fournissant des descripteurs de service à la collection de services.</span><span class="sxs-lookup"><span data-stu-id="e2785-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="e2785-149">L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation `DataAccess`concrète :</span><span class="sxs-lookup"><span data-stu-id="e2785-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="e2785-150">Durée de vie du service</span><span class="sxs-lookup"><span data-stu-id="e2785-150">Service lifetime</span></span>

<span data-ttu-id="e2785-151">Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="e2785-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="e2785-152">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="e2785-152">Lifetime</span></span> | <span data-ttu-id="e2785-153">Description</span><span class="sxs-lookup"><span data-stu-id="e2785-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor<span data-ttu-id="e2785-154">Les applications webassembly n’ont pas actuellement de concept d’étendues DI.</span><span class="sxs-lookup"><span data-stu-id="e2785-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="e2785-155">`Scoped`-les services inscrits se `Singleton` comportent comme des services.</span><span class="sxs-lookup"><span data-stu-id="e2785-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="e2785-156">Toutefois, le Blazor modèle d’hébergement de serveur `Scoped` prend en charge la durée de vie.</span><span class="sxs-lookup"><span data-stu-id="e2785-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="e2785-157">Dans Blazor les applications serveur, l’inscription d’un service étendu est limitée à la *connexion*.</span><span class="sxs-lookup"><span data-stu-id="e2785-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="e2785-158">Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="e2785-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="e2785-159">DI crée une *seule instance* du service.</span><span class="sxs-lookup"><span data-stu-id="e2785-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="e2785-160">Tous les composants qui `Singleton` requièrent un service reçoivent une instance du même service.</span><span class="sxs-lookup"><span data-stu-id="e2785-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="e2785-161">Chaque fois qu’un composant obtient une instance d' `Transient` un service à partir du conteneur de service, il reçoit une *nouvelle instance* du service.</span><span class="sxs-lookup"><span data-stu-id="e2785-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="e2785-162">Le système DI est basé sur le système DI dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2785-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="e2785-163">Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e2785-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="e2785-164">Demander un service dans un composant</span><span class="sxs-lookup"><span data-stu-id="e2785-164">Request a service in a component</span></span>

<span data-ttu-id="e2785-165">Une fois les services ajoutés à la collection de services, injectez les services dans les [ \@](xref:mvc/views/razor#inject) composants à l’aide de la directive Inject Razor .</span><span class="sxs-lookup"><span data-stu-id="e2785-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="e2785-166">`@inject`a deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="e2785-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="e2785-167">Tapez &ndash; le type du service à injecter.</span><span class="sxs-lookup"><span data-stu-id="e2785-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="e2785-168">Propriété &ndash; nom de la propriété qui reçoit le service d’application injecté.</span><span class="sxs-lookup"><span data-stu-id="e2785-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="e2785-169">La propriété ne nécessite pas de création manuelle.</span><span class="sxs-lookup"><span data-stu-id="e2785-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="e2785-170">Le compilateur crée la propriété.</span><span class="sxs-lookup"><span data-stu-id="e2785-170">The compiler creates the property.</span></span>

<span data-ttu-id="e2785-171">Pour plus d'informations, consultez <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e2785-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="e2785-172">Utilisez plusieurs `@inject` instructions pour injecter différents services.</span><span class="sxs-lookup"><span data-stu-id="e2785-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="e2785-173">L'exemple suivant montre comment utiliser `@inject`.</span><span class="sxs-lookup"><span data-stu-id="e2785-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="e2785-174">Le service qui `Services.IDataAccess` implémente est injecté dans la propriété `DataRepository`du composant.</span><span class="sxs-lookup"><span data-stu-id="e2785-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="e2785-175">Notez la manière dont le code utilise uniquement `IDataAccess` l’abstraction :</span><span class="sxs-lookup"><span data-stu-id="e2785-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="e2785-176">En interne, la propriété générée (`DataRepository`) utilise l' `InjectAttribute` attribut.</span><span class="sxs-lookup"><span data-stu-id="e2785-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="e2785-177">En règle générale, cet attribut n’est pas utilisé directement.</span><span class="sxs-lookup"><span data-stu-id="e2785-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="e2785-178">Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base `InjectAttribute`, ajoutez manuellement le :</span><span class="sxs-lookup"><span data-stu-id="e2785-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="e2785-179">Dans les composants dérivés de la classe de `@inject` base, la directive n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="e2785-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="e2785-180">Le `InjectAttribute` de la classe de base est suffisant :</span><span class="sxs-lookup"><span data-stu-id="e2785-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="e2785-181">Utiliser DI dans les services</span><span class="sxs-lookup"><span data-stu-id="e2785-181">Use DI in services</span></span>

<span data-ttu-id="e2785-182">Les services complexes peuvent nécessiter des services supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="e2785-182">Complex services might require additional services.</span></span> <span data-ttu-id="e2785-183">Dans l’exemple précédent, `DataAccess` peut nécessiter `HttpClient` le service par défaut.</span><span class="sxs-lookup"><span data-stu-id="e2785-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="e2785-184">`@inject`(ou) `InjectAttribute`n’est pas disponible pour une utilisation dans les services.</span><span class="sxs-lookup"><span data-stu-id="e2785-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="e2785-185">L' *injection de constructeur* doit être utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="e2785-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="e2785-186">Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service.</span><span class="sxs-lookup"><span data-stu-id="e2785-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="e2785-187">Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.</span><span class="sxs-lookup"><span data-stu-id="e2785-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="e2785-188">Conditions préalables pour l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="e2785-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="e2785-189">Un constructeur doit exister dont les arguments peuvent tous être remplis par DI.</span><span class="sxs-lookup"><span data-stu-id="e2785-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="e2785-190">Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="e2785-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="e2785-191">Le constructeur applicable doit être *public*.</span><span class="sxs-lookup"><span data-stu-id="e2785-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="e2785-192">Un constructeur applicable doit exister.</span><span class="sxs-lookup"><span data-stu-id="e2785-192">One applicable constructor must exist.</span></span> <span data-ttu-id="e2785-193">En cas d’ambiguïté, DI lève une exception.</span><span class="sxs-lookup"><span data-stu-id="e2785-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="e2785-194">Classes de composants de base de l’utilitaire pour gérer une étendue DI</span><span class="sxs-lookup"><span data-stu-id="e2785-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="e2785-195">Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="e2785-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="e2785-196">Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI.</span><span class="sxs-lookup"><span data-stu-id="e2785-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="e2785-197">Dans Blazor les applications serveur, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu.</span><span class="sxs-lookup"><span data-stu-id="e2785-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="e2785-198">Dans Blazor les applications webassembly, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.</span><span class="sxs-lookup"><span data-stu-id="e2785-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="e2785-199">Une approche qui limite la durée de vie Blazor d’un service dans les `OwningComponentBase` applications est l’utilisation du type.</span><span class="sxs-lookup"><span data-stu-id="e2785-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="e2785-200">`OwningComponentBase`est un type abstrait dérivé de `ComponentBase` qui crée une étendue di correspondant à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="e2785-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="e2785-201">À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant.</span><span class="sxs-lookup"><span data-stu-id="e2785-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="e2785-202">Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés.</span><span class="sxs-lookup"><span data-stu-id="e2785-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="e2785-203">Cela peut être utile pour les services qui :</span><span class="sxs-lookup"><span data-stu-id="e2785-203">This can be useful for services that:</span></span>

* <span data-ttu-id="e2785-204">Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="e2785-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="e2785-205">Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="e2785-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="e2785-206">Deux versions du `OwningComponentBase` type sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="e2785-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="e2785-207">`OwningComponentBase`est un enfant abstrait et jetable du `ComponentBase` type avec une propriété protégée `ScopedServices` de type. `IServiceProvider`</span><span class="sxs-lookup"><span data-stu-id="e2785-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="e2785-208">Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="e2785-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="e2785-209">Les services d’injection de services injectés `@inject` dans le `InjectAttribute` composant`[Inject]`à l’aide de ou () ne sont pas créés dans l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="e2785-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="e2785-210">Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide `ScopedServices.GetRequiredService` de ou `ScopedServices.GetService`de.</span><span class="sxs-lookup"><span data-stu-id="e2785-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="e2785-211">Les dépendances de tous `ScopedServices` les services résolus à l’aide du fournisseur sont fournies à partir de cette même étendue.</span><span class="sxs-lookup"><span data-stu-id="e2785-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="e2785-212">`OwningComponentBase<T>`dérive de `OwningComponentBase` et ajoute une propriété `Service` qui retourne une instance de `T` à partir du fournisseur di étendu.</span><span class="sxs-lookup"><span data-stu-id="e2785-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="e2785-213">Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une `IServiceProvider` instance de lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="e2785-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="e2785-214">La `ScopedServices` propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="e2785-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="e2785-215">Utilisation de l’Entity Framework DbContext à partir de DI</span><span class="sxs-lookup"><span data-stu-id="e2785-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="e2785-216">Un type de service commun à récupérer à partir de DI dans Web Apps est Entity Framework `DbContext` les objets (EF).</span><span class="sxs-lookup"><span data-stu-id="e2785-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="e2785-217">L’inscription des services `IServiceCollection.AddDbContext` EF à `DbContext` l’aide de ajoute en tant que service étendu par défaut.</span><span class="sxs-lookup"><span data-stu-id="e2785-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="e2785-218">L’inscription en tant que service étendu peut entraîner des problèmes Blazor dans les applications, `DbContext` car elle entraîne une longue durée de vie des instances et leur partage sur l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="e2785-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="e2785-219">`DbContext`n’est pas thread-safe et ne doit pas être utilisé simultanément.</span><span class="sxs-lookup"><span data-stu-id="e2785-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="e2785-220">Selon l’application, l’utilisation `OwningComponentBase` de pour limiter l’étendue d’un `DbContext` à un seul composant *peut* résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="e2785-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="e2785-221">Si un composant n’utilise pas `DbContext` en parallèle, le fait de dériver `OwningComponentBase` le composant de et `DbContext` de `ScopedServices` récupérer le de est suffisant, car il garantit que :</span><span class="sxs-lookup"><span data-stu-id="e2785-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="e2785-222">Les composants distincts ne partagent `DbContext`pas un.</span><span class="sxs-lookup"><span data-stu-id="e2785-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="e2785-223">La `DbContext` fonction vit uniquement tant que le composant en dépend.</span><span class="sxs-lookup"><span data-stu-id="e2785-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="e2785-224">Si un seul composant peut utiliser un `DbContext` en même temps (par exemple, chaque fois qu’un utilisateur sélectionne un bouton), même `OwningComponentBase` l’utilisation de n’évite pas les problèmes liés à des opérations EF simultanées.</span><span class="sxs-lookup"><span data-stu-id="e2785-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="e2785-225">Dans ce cas, utilisez un différent `DbContext` pour chaque opération EF logique.</span><span class="sxs-lookup"><span data-stu-id="e2785-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="e2785-226">Utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="e2785-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="e2785-227">Créez `DbContext` directement à l' `DbContextOptions<TContext>` aide de comme argument, qui peut être récupéré à partir de di et est thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e2785-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="e2785-228">Inscrivez le `DbContext` dans le conteneur de service avec une durée de vie transitoire :</span><span class="sxs-lookup"><span data-stu-id="e2785-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="e2785-229">Lors de l’inscription du contexte `ServiceLifetime.Transient`, utilisez.</span><span class="sxs-lookup"><span data-stu-id="e2785-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="e2785-230">La `AddDbContext` méthode d’extension accepte deux paramètres facultatifs `ServiceLifetime`de type.</span><span class="sxs-lookup"><span data-stu-id="e2785-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="e2785-231">Pour utiliser cette approche, seul le `contextLifetime` paramètre doit être `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="e2785-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="e2785-232">`optionsLifetime`peut conserver sa valeur par défaut `ServiceLifetime.Scoped`.</span><span class="sxs-lookup"><span data-stu-id="e2785-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="e2785-233">Le temporaire `DbContext` peut être injecté comme normal (à l' `@inject`aide de) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle.</span><span class="sxs-lookup"><span data-stu-id="e2785-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="e2785-234">Ceux qui peuvent exécuter plusieurs opérations EF simultanément peuvent demander des `DbContext` objets distincts pour chaque opération parallèle `IServiceProvider.GetRequiredService`à l’aide de.</span><span class="sxs-lookup"><span data-stu-id="e2785-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="e2785-235">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e2785-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
