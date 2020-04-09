---
title: injection de dépendance Blazor ASP.NET Core
author: guardrex
description: Découvrez Blazor comment les applications peuvent injecter des services dans des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658073"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="10efe-103">injection de dépendance ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="10efe-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="10efe-104">Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="10efe-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="10efe-105">Blazor soutient [l’injection de dépendance (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="10efe-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="10efe-106">Les applications peuvent utiliser des services intégrés en les injectant dans des composants.</span><span class="sxs-lookup"><span data-stu-id="10efe-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="10efe-107">Les applications peuvent également définir et enregistrer des services personnalisés et les rendre disponibles dans toute l’application via DI.</span><span class="sxs-lookup"><span data-stu-id="10efe-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="10efe-108">DI est une technique d’accès aux services configurés dans un emplacement central.</span><span class="sxs-lookup"><span data-stu-id="10efe-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="10efe-109">Cela peut être utile dans les applications Blazor pour :</span><span class="sxs-lookup"><span data-stu-id="10efe-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="10efe-110">Partagez une seule instance d’une classe de service sur de nombreux composants, connu sous le nom de service *singleton.*</span><span class="sxs-lookup"><span data-stu-id="10efe-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="10efe-111">Découpler les composants des classes de service en béton en utilisant des abstractions de référence.</span><span class="sxs-lookup"><span data-stu-id="10efe-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="10efe-112">Par exemple, envisagez une interface `IDataAccess` pour accéder aux données de l’application.</span><span class="sxs-lookup"><span data-stu-id="10efe-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="10efe-113">L’interface est implémentée par une classe concrète `DataAccess` et enregistrée comme service dans le conteneur de service de l’application.</span><span class="sxs-lookup"><span data-stu-id="10efe-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="10efe-114">Lorsqu’un composant utilise `IDataAccess` DI pour recevoir une implémentation, le composant n’est pas couplé au type concret.</span><span class="sxs-lookup"><span data-stu-id="10efe-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="10efe-115">La mise en œuvre peut être échangée, peut-être pour une mise en œuvre simulée dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="10efe-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="10efe-116">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="10efe-116">Default services</span></span>

<span data-ttu-id="10efe-117">Les services par défaut sont automatiquement ajoutés à la collecte de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="10efe-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="10efe-118">Service</span><span class="sxs-lookup"><span data-stu-id="10efe-118">Service</span></span> | <span data-ttu-id="10efe-119">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="10efe-119">Lifetime</span></span> | <span data-ttu-id="10efe-120">Description</span><span class="sxs-lookup"><span data-stu-id="10efe-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="10efe-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="10efe-121">Singleton</span></span> | <span data-ttu-id="10efe-122">Fournit des méthodes pour envoyer des demandes de HTTP et recevoir des réponses HTTP à partir d’une ressource identifiée par un URI.</span><span class="sxs-lookup"><span data-stu-id="10efe-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="10efe-123">L’exemple `HttpClient` d’une application Blazor WebAssembly utilise le navigateur pour gérer le trafic HTTP en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="10efe-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="10efe-124">Les applications Blazor Server `HttpClient` n’incluent pas un service configuré par défaut.</span><span class="sxs-lookup"><span data-stu-id="10efe-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="10efe-125">Fournir `HttpClient` une application Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="10efe-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="10efe-126">Pour plus d’informations, consultez <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="10efe-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="10efe-127">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="10efe-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="10efe-128">Portée (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="10efe-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="10efe-129">Représente une instance d’un temps d’exécution JavaScript où les appels JavaScript sont envoyés.</span><span class="sxs-lookup"><span data-stu-id="10efe-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="10efe-130">Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="10efe-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="10efe-131">Singleton (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="10efe-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="10efe-132">Portée (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="10efe-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="10efe-133">Contient des aides pour travailler avec les ITI et l’état de navigation.</span><span class="sxs-lookup"><span data-stu-id="10efe-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="10efe-134">Pour plus d’informations, voir [URI et les aides d’état de navigation](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="10efe-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="10efe-135">Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut répertoriés dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="10efe-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="10efe-136">Si vous utilisez un fournisseur de services personnalisés et que vous avez besoin de l’un des services indiqués dans le tableau, ajoutez les services requis au nouveau fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="10efe-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="10efe-137">Ajouter des services à une application</span><span class="sxs-lookup"><span data-stu-id="10efe-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="10efe-138">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="10efe-138">Blazor WebAssembly</span></span>

<span data-ttu-id="10efe-139">Configurer les services pour la `Main` collection de services de l’application dans la méthode de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="10efe-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="10efe-140">Dans l’exemple `MyDependency` suivant, la `IMyDependency`mise en œuvre est enregistrée pour :</span><span class="sxs-lookup"><span data-stu-id="10efe-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="10efe-141">Une fois l’hôte construit, les services peuvent être consultés à partir de la portée DI racine avant que tous les composants soient rendus.</span><span class="sxs-lookup"><span data-stu-id="10efe-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="10efe-142">Cela peut être utile pour exécuter la logique de initialisation avant de rendre le contenu:</span><span class="sxs-lookup"><span data-stu-id="10efe-142">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="10efe-143">L’hôte fournit également une instance de configuration centrale pour l’application.</span><span class="sxs-lookup"><span data-stu-id="10efe-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="10efe-144">S’appuyant sur l’exemple précédent, l’URL du service météorologique est transmise d’une source `InitializeWeatherAsync`de configuration par défaut (par exemple, *appsettings.json*) à :</span><span class="sxs-lookup"><span data-stu-id="10efe-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="10efe-145">Serveur Blazor</span><span class="sxs-lookup"><span data-stu-id="10efe-145">Blazor Server</span></span>

<span data-ttu-id="10efe-146">Après la création d’une nouvelle application, examinez la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="10efe-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="10efe-147">La `ConfigureServices` méthode est <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>adoptée un , qui est une<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>liste d’objets descripteur de service ( ).</span><span class="sxs-lookup"><span data-stu-id="10efe-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="10efe-148">Les services sont ajoutés en fournissant des descripteurs de service à la collection de services.</span><span class="sxs-lookup"><span data-stu-id="10efe-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="10efe-149">L’exemple suivant montre le `IDataAccess` concept avec `DataAccess`l’interface et sa mise en œuvre concrète :</span><span class="sxs-lookup"><span data-stu-id="10efe-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="10efe-150">Durée de vie du service</span><span class="sxs-lookup"><span data-stu-id="10efe-150">Service lifetime</span></span>

<span data-ttu-id="10efe-151">Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="10efe-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="10efe-152">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="10efe-152">Lifetime</span></span> | <span data-ttu-id="10efe-153">Description</span><span class="sxs-lookup"><span data-stu-id="10efe-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="10efe-154">Les applications WebAssembly n’ont pas actuellement de concept de portée DI.</span><span class="sxs-lookup"><span data-stu-id="10efe-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="10efe-155">`Scoped`les services enregistrés `Singleton` se comportent comme des services.</span><span class="sxs-lookup"><span data-stu-id="10efe-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="10efe-156">Cependant, Blazor le modèle d’hébergement Server prend en charge la `Scoped` durée de vie.</span><span class="sxs-lookup"><span data-stu-id="10efe-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="10efe-157">Dans Blazor les applications Server, une inscription de service étendue est étendue à la *connexion*.</span><span class="sxs-lookup"><span data-stu-id="10efe-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="10efe-158">Pour cette raison, l’utilisation de services à portée est préférable pour les services qui devraient être étendues à l’utilisateur actuel, même si l’intention actuelle est d’exécuter le côté client dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="10efe-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="10efe-159">DI crée une *seule instance* du service.</span><span class="sxs-lookup"><span data-stu-id="10efe-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="10efe-160">Tous les composants `Singleton` nécessitant un service reçoivent une instance du même service.</span><span class="sxs-lookup"><span data-stu-id="10efe-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="10efe-161">Chaque fois qu’un composant `Transient` obtient une instance d’un service à partir du conteneur de service, il reçoit une *nouvelle instance* du service.</span><span class="sxs-lookup"><span data-stu-id="10efe-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="10efe-162">Le système DI est basé sur le système DI dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="10efe-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="10efe-163">Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="10efe-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="10efe-164">Demander un service dans un composant</span><span class="sxs-lookup"><span data-stu-id="10efe-164">Request a service in a component</span></span>

<span data-ttu-id="10efe-165">Une fois que les services sont ajoutés à la [ \@](xref:mvc/views/razor#inject) collecte de services, injectez les services dans les composants à l’aide de la directive Razor injectable.</span><span class="sxs-lookup"><span data-stu-id="10efe-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="10efe-166">`@inject`a deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="10efe-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="10efe-167">Tapez &ndash; le type de service à injecter.</span><span class="sxs-lookup"><span data-stu-id="10efe-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="10efe-168">Propriété &ndash; Le nom de la propriété recevant le service d’application injecté.</span><span class="sxs-lookup"><span data-stu-id="10efe-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="10efe-169">La propriété ne nécessite pas de création manuelle.</span><span class="sxs-lookup"><span data-stu-id="10efe-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="10efe-170">Le compilateur crée la propriété.</span><span class="sxs-lookup"><span data-stu-id="10efe-170">The compiler creates the property.</span></span>

<span data-ttu-id="10efe-171">Pour plus d’informations, consultez <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="10efe-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="10efe-172">Utilisez `@inject` plusieurs instructions pour injecter différents services.</span><span class="sxs-lookup"><span data-stu-id="10efe-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="10efe-173">L'exemple suivant montre comment utiliser `@inject`.</span><span class="sxs-lookup"><span data-stu-id="10efe-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="10efe-174">La mise `Services.IDataAccess` en œuvre du `DataRepository`service est injectée dans la propriété du composant.</span><span class="sxs-lookup"><span data-stu-id="10efe-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="10efe-175">Notez comment le code `IDataAccess` utilise uniquement l’abstraction :</span><span class="sxs-lookup"><span data-stu-id="10efe-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="10efe-176">En interne, la`DataRepository`propriété `InjectAttribute` générée ( ) utilise l’attribut.</span><span class="sxs-lookup"><span data-stu-id="10efe-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="10efe-177">Typiquement, cet attribut n’est pas utilisé directement.</span><span class="sxs-lookup"><span data-stu-id="10efe-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="10efe-178">Si une classe de base est nécessaire pour les composants et les `InjectAttribute`propriétés injectées sont également nécessaires pour la classe de base, ajouter manuellement le :</span><span class="sxs-lookup"><span data-stu-id="10efe-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="10efe-179">Dans les composants dérivés de `@inject` la classe de base, la directive n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="10efe-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="10efe-180">La `InjectAttribute` classe de base est suffisante :</span><span class="sxs-lookup"><span data-stu-id="10efe-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="10efe-181">Utiliser DI dans les services</span><span class="sxs-lookup"><span data-stu-id="10efe-181">Use DI in services</span></span>

<span data-ttu-id="10efe-182">Les services complexes peuvent nécessiter des services supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="10efe-182">Complex services might require additional services.</span></span> <span data-ttu-id="10efe-183">Dans l’exemple `DataAccess` précédent, `HttpClient` peut nécessiter le service par défaut.</span><span class="sxs-lookup"><span data-stu-id="10efe-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="10efe-184">`@inject`(ou `InjectAttribute`le ) n’est pas disponible pour une utilisation dans les services.</span><span class="sxs-lookup"><span data-stu-id="10efe-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="10efe-185">*L’injection de constructeur* doit être utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="10efe-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="10efe-186">Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service.</span><span class="sxs-lookup"><span data-stu-id="10efe-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="10efe-187">Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.</span><span class="sxs-lookup"><span data-stu-id="10efe-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="10efe-188">Conditions préalables à l’injection de constructeurs :</span><span class="sxs-lookup"><span data-stu-id="10efe-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="10efe-189">Un constructeur doit exister dont les arguments peuvent tous être remplis par DI.</span><span class="sxs-lookup"><span data-stu-id="10efe-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="10efe-190">Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="10efe-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="10efe-191">Le constructeur applicable doit être *public*.</span><span class="sxs-lookup"><span data-stu-id="10efe-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="10efe-192">Un constructeur applicable doit exister.</span><span class="sxs-lookup"><span data-stu-id="10efe-192">One applicable constructor must exist.</span></span> <span data-ttu-id="10efe-193">En cas d’ambiguïté, DI jette une exception.</span><span class="sxs-lookup"><span data-stu-id="10efe-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="10efe-194">Classes de composants de base d’utilité pour gérer une portée DI</span><span class="sxs-lookup"><span data-stu-id="10efe-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="10efe-195">Dans ASP.NET applications Core, les services à portée sont généralement étendues à la demande actuelle.</span><span class="sxs-lookup"><span data-stu-id="10efe-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="10efe-196">Une fois la demande terminée, tout service d’envergure ou de passage est éliminé par le système DI.</span><span class="sxs-lookup"><span data-stu-id="10efe-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="10efe-197">Dans Blazor les applications Server, la portée de la demande dure pendant la durée de la connexion client, ce qui peut entraîner des services transitoires et d’envergure qui vivent beaucoup plus longtemps que prévu.</span><span class="sxs-lookup"><span data-stu-id="10efe-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="10efe-198">Dans Blazor les applications WebAssembly, les services enregistrés avec une durée de vie étendue sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services de portée dans les applications ASP.NET core typiques.</span><span class="sxs-lookup"><span data-stu-id="10efe-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="10efe-199">Une approche qui limite Blazor la durée de `OwningComponentBase` vie d’un service dans les applications est l’utilisation du type.</span><span class="sxs-lookup"><span data-stu-id="10efe-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="10efe-200">`OwningComponentBase`est un type `ComponentBase` abstrait dérivé de celui qui crée une portée DI correspondant à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="10efe-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="10efe-201">En utilisant cette portée, il est possible d’utiliser des services DI avec une durée de vie étendue et de les faire vivre aussi longtemps que le composant.</span><span class="sxs-lookup"><span data-stu-id="10efe-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="10efe-202">Lorsque le composant est détruit, les services du fournisseur de services à portée du composant sont également éliminés.</span><span class="sxs-lookup"><span data-stu-id="10efe-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="10efe-203">Cela peut être utile pour les services qui :</span><span class="sxs-lookup"><span data-stu-id="10efe-203">This can be useful for services that:</span></span>

* <span data-ttu-id="10efe-204">Devrait être réutilisé dans un composant, car la durée de vie transitoire est inappropriée.</span><span class="sxs-lookup"><span data-stu-id="10efe-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="10efe-205">Ne devrait pas être partagé entre les composants, car la durée de vie du singleton est inappropriée.</span><span class="sxs-lookup"><span data-stu-id="10efe-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="10efe-206">Deux versions `OwningComponentBase` du type sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="10efe-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="10efe-207">`OwningComponentBase`est un enfant abstrait `ComponentBase` et jetable `ScopedServices` du `IServiceProvider`type avec une propriété protégée de type .</span><span class="sxs-lookup"><span data-stu-id="10efe-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="10efe-208">Ce fournisseur peut être utilisé pour résoudre les services qui sont étendues à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="10efe-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="10efe-209">Les services d' `@inject` DI `InjectAttribute` `[Inject]`injectés dans le composant à l’aide ou le ( ) ne sont pas créés dans la portée du composant.</span><span class="sxs-lookup"><span data-stu-id="10efe-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="10efe-210">Pour utiliser la portée du composant, `ScopedServices.GetRequiredService` les `ScopedServices.GetService`services doivent être résolus à l’aide ou .</span><span class="sxs-lookup"><span data-stu-id="10efe-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="10efe-211">Tous les services `ScopedServices` résolus à l’aide du fournisseur ont leurs dépendances fournies à partir de cette même portée.</span><span class="sxs-lookup"><span data-stu-id="10efe-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="10efe-212">`OwningComponentBase<T>`tire et `OwningComponentBase` ajoute une `Service` propriété qui `T` renvoie une instance du fournisseur DE DI à portée.</span><span class="sxs-lookup"><span data-stu-id="10efe-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="10efe-213">Ce type est un moyen pratique d’accéder `IServiceProvider` aux services d’envergure sans utiliser un exemple de lorsqu’il y a un service principal que l’application nécessite à partir du conteneur DI en utilisant la portée du composant.</span><span class="sxs-lookup"><span data-stu-id="10efe-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="10efe-214">La `ScopedServices` propriété est disponible, de sorte que l’application peut obtenir des services d’autres types, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="10efe-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="10efe-215">Utilisation de l’entité Framework DbContext de DI</span><span class="sxs-lookup"><span data-stu-id="10efe-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="10efe-216">Un type de service commun à récupérer à partir `DbContext` de DI dans les applications Web est Entity Framework (EF) objets.</span><span class="sxs-lookup"><span data-stu-id="10efe-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="10efe-217">L’enregistrement des `IServiceCollection.AddDbContext` services `DbContext` EF en utilisant ajoute le service comme un service d’envergure par défaut.</span><span class="sxs-lookup"><span data-stu-id="10efe-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="10efe-218">L’inscription en tant que service Blazor à portée `DbContext` peut entraîner des problèmes dans les applications, car elle entraîne des cas à longue durée de vie et partagés sur l’application.</span><span class="sxs-lookup"><span data-stu-id="10efe-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="10efe-219">`DbContext`n’est pas sans fil et ne doit pas être utilisé simultanément.</span><span class="sxs-lookup"><span data-stu-id="10efe-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="10efe-220">Selon l’application, `OwningComponentBase` l’utilisation pour `DbContext` limiter la portée d’un composant à un seul composant *peut* résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="10efe-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="10efe-221">Si un composant n’utilise `DbContext` pas un en parallèle, le fait de dérimer le composant et de `OwningComponentBase` la `DbContext` récupérer `ScopedServices` est suffisant parce qu’il garantit que :</span><span class="sxs-lookup"><span data-stu-id="10efe-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="10efe-222">Les composants distincts ne `DbContext`partagent pas un .</span><span class="sxs-lookup"><span data-stu-id="10efe-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="10efe-223">La `DbContext` vie seulement aussi longtemps que le composant en fonction de lui.</span><span class="sxs-lookup"><span data-stu-id="10efe-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="10efe-224">Si un seul composant `DbContext` peut utiliser un composant en même temps (par `OwningComponentBase` exemple, chaque fois qu’un utilisateur sélectionne un bouton), même en utilisant n’évite pas les problèmes avec les opérations EF simultanées.</span><span class="sxs-lookup"><span data-stu-id="10efe-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="10efe-225">Dans ce cas, `DbContext` utilisez un autre pour chaque opération EF logique.</span><span class="sxs-lookup"><span data-stu-id="10efe-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="10efe-226">Utilisez l’une ou l’autre des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="10efe-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="10efe-227">Créez `DbContext` l’utilisation `DbContextOptions<TContext>` directe comme argument, qui peut être récupéré à partir de DI et est sans danger de thread.</span><span class="sxs-lookup"><span data-stu-id="10efe-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

* <span data-ttu-id="10efe-228">Enregistrez `DbContext` le conteneur dans le conteneur de service avec une durée de vie transitoire :</span><span class="sxs-lookup"><span data-stu-id="10efe-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="10efe-229">Lors de l’enregistrement `ServiceLifetime.Transient`du contexte, utilisez .</span><span class="sxs-lookup"><span data-stu-id="10efe-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="10efe-230">La `AddDbContext` méthode d’extension prend `ServiceLifetime`deux paramètres facultatifs de type .</span><span class="sxs-lookup"><span data-stu-id="10efe-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="10efe-231">Pour utiliser cette approche, seul `contextLifetime` `ServiceLifetime.Transient`le paramètre doit être .</span><span class="sxs-lookup"><span data-stu-id="10efe-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="10efe-232">`optionsLifetime`peut conserver sa `ServiceLifetime.Scoped`valeur par défaut de .</span><span class="sxs-lookup"><span data-stu-id="10efe-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="10efe-233">Le transitoire `DbContext` peut être injecté `@inject`comme d’habitude (en utilisant) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle.</span><span class="sxs-lookup"><span data-stu-id="10efe-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="10efe-234">Ceux qui peuvent effectuer plusieurs opérations `DbContext` EF simultanément peuvent `IServiceProvider.GetRequiredService`demander des objets distincts pour chaque opération parallèle à l’aide de .</span><span class="sxs-lookup"><span data-stu-id="10efe-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
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

## <a name="additional-resources"></a><span data-ttu-id="10efe-235">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="10efe-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
