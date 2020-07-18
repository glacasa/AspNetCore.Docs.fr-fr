---
title: ASP.NET Core l' Blazor injection de dépendances
author: guardrex
description: Découvrez comment les Blazor applications peuvent injecter des services dans des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 07fe7d4b64c84956be44e7d3ac0b1d8687b085c6
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445162"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="a41a6-103">ASP.NET Core l' Blazor injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="a41a6-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="a41a6-104">Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="a41a6-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="a41a6-105">prend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a41a6-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a41a6-106">Les applications peuvent utiliser des services intégrés en les injectant dans des composants.</span><span class="sxs-lookup"><span data-stu-id="a41a6-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="a41a6-107">Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.</span><span class="sxs-lookup"><span data-stu-id="a41a6-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="a41a6-108">La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central.</span><span class="sxs-lookup"><span data-stu-id="a41a6-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="a41a6-109">Cela peut être utile dans les Blazor applications pour :</span><span class="sxs-lookup"><span data-stu-id="a41a6-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="a41a6-110">Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .</span><span class="sxs-lookup"><span data-stu-id="a41a6-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="a41a6-111">Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence.</span><span class="sxs-lookup"><span data-stu-id="a41a6-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="a41a6-112">Par exemple, considérez une interface `IDataAccess` pour accéder aux données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a41a6-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="a41a6-113">L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="a41a6-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="a41a6-114">Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret.</span><span class="sxs-lookup"><span data-stu-id="a41a6-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="a41a6-115">L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="a41a6-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="a41a6-116">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="a41a6-116">Default services</span></span>

<span data-ttu-id="a41a6-117">Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="a41a6-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="a41a6-118">Service</span><span class="sxs-lookup"><span data-stu-id="a41a6-118">Service</span></span> | <span data-ttu-id="a41a6-119">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="a41a6-119">Lifetime</span></span> | <span data-ttu-id="a41a6-120">Description</span><span class="sxs-lookup"><span data-stu-id="a41a6-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="a41a6-121">Délimité</span><span class="sxs-lookup"><span data-stu-id="a41a6-121">Scoped</span></span> | <span data-ttu-id="a41a6-122">Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.</span><span class="sxs-lookup"><span data-stu-id="a41a6-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="a41a6-123">L’instance de <xref:System.Net.Http.HttpClient> dans une Blazor WebAssembly application utilise le navigateur pour gérer le trafic HTTP en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="a41a6-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor Server<span data-ttu-id="a41a6-124">par défaut, les applications n’incluent pas une <xref:System.Net.Http.HttpClient> configuration en tant que service.</span><span class="sxs-lookup"><span data-stu-id="a41a6-124"> apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="a41a6-125">Fournissez un <xref:System.Net.Http.HttpClient> à une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="a41a6-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="a41a6-126">Pour plus d’informations, consultez <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="a41a6-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="a41a6-127">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="a41a6-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a41a6-128">Étendu ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="a41a6-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="a41a6-129">Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués.</span><span class="sxs-lookup"><span data-stu-id="a41a6-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="a41a6-130">Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="a41a6-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="a41a6-131">Singleton ( Blazor WebAssembly )</span><span class="sxs-lookup"><span data-stu-id="a41a6-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="a41a6-132">Étendu ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="a41a6-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="a41a6-133">Contient des assistances pour l’utilisation des URI et de l’état de navigation.</span><span class="sxs-lookup"><span data-stu-id="a41a6-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="a41a6-134">Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="a41a6-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="a41a6-135">Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="a41a6-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="a41a6-136">Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="a41a6-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="a41a6-137">Ajouter des services à une application</span><span class="sxs-lookup"><span data-stu-id="a41a6-137">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="a41a6-138">Configurez les services de la collection de services de l’application dans la `Main` méthode de `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="a41a6-138">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="a41a6-139">Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite pour `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="a41a6-139">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="a41a6-140">Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="a41a6-140">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="a41a6-141">Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :</span><span class="sxs-lookup"><span data-stu-id="a41a6-141">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="a41a6-142">L’hôte fournit également une instance de configuration centrale pour l’application.</span><span class="sxs-lookup"><span data-stu-id="a41a6-142">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="a41a6-143">En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, `appsettings.json` ) à `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="a41a6-143">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

<span data-ttu-id="a41a6-144">Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="a41a6-144">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="a41a6-145"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Une <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , qui est une liste d’objets descripteurs de service (), est passée à la méthode <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-145">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="a41a6-146">Les services sont ajoutés dans la `ConfigureServices` méthode en fournissant des descripteurs de service à la collection de services.</span><span class="sxs-lookup"><span data-stu-id="a41a6-146">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="a41a6-147">L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation concrète `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="a41a6-147">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="a41a6-148">Durée de vie du service</span><span class="sxs-lookup"><span data-stu-id="a41a6-148">Service lifetime</span></span>

<span data-ttu-id="a41a6-149">Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="a41a6-149">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="a41a6-150">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="a41a6-150">Lifetime</span></span> | <span data-ttu-id="a41a6-151">Description</span><span class="sxs-lookup"><span data-stu-id="a41a6-151">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly<span data-ttu-id="a41a6-152">les applications n’ont pas actuellement de concept d’étendues DI.</span><span class="sxs-lookup"><span data-stu-id="a41a6-152"> apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="a41a6-153">`Scoped`-les services inscrits se comportent comme des `Singleton` services.</span><span class="sxs-lookup"><span data-stu-id="a41a6-153">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="a41a6-154">Toutefois, le Blazor Server modèle d’hébergement prend en charge la `Scoped` durée de vie.</span><span class="sxs-lookup"><span data-stu-id="a41a6-154">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="a41a6-155">Dans Blazor Server les applications, l’inscription d’un service étendu est limitée à la *connexion*.</span><span class="sxs-lookup"><span data-stu-id="a41a6-155">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="a41a6-156">Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="a41a6-156">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="a41a6-157">DI crée une *seule instance* du service.</span><span class="sxs-lookup"><span data-stu-id="a41a6-157">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="a41a6-158">Tous les composants qui requièrent un `Singleton` service reçoivent une instance du même service.</span><span class="sxs-lookup"><span data-stu-id="a41a6-158">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="a41a6-159">Chaque fois qu’un composant obtient une instance d’un `Transient` service à partir du conteneur de service, il reçoit une *nouvelle instance* du service.</span><span class="sxs-lookup"><span data-stu-id="a41a6-159">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="a41a6-160">Le système DI est basé sur le système DI dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a41a6-160">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="a41a6-161">Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a41a6-161">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="a41a6-162">Demander un service dans un composant</span><span class="sxs-lookup"><span data-stu-id="a41a6-162">Request a service in a component</span></span>

<span data-ttu-id="a41a6-163">Une fois les services ajoutés à la collection de services, injectez les services dans les composants à l’aide de la directive [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="a41a6-163">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="a41a6-164">[`@inject`](xref:mvc/views/razor#inject)a deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="a41a6-164">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="a41a6-165">Type : type du service à injecter.</span><span class="sxs-lookup"><span data-stu-id="a41a6-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="a41a6-166">Propriété : nom de la propriété qui reçoit le service d’application injecté.</span><span class="sxs-lookup"><span data-stu-id="a41a6-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="a41a6-167">La propriété ne nécessite pas de création manuelle.</span><span class="sxs-lookup"><span data-stu-id="a41a6-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="a41a6-168">Le compilateur crée la propriété.</span><span class="sxs-lookup"><span data-stu-id="a41a6-168">The compiler creates the property.</span></span>

<span data-ttu-id="a41a6-169">Pour plus d’informations, consultez <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a41a6-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="a41a6-170">Utilisez plusieurs [`@inject`](xref:mvc/views/razor#inject) instructions pour injecter différents services.</span><span class="sxs-lookup"><span data-stu-id="a41a6-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="a41a6-171">L’exemple suivant montre comment utiliser [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="a41a6-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="a41a6-172">Le service qui implémente `Services.IDataAccess` est injecté dans la propriété du composant `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="a41a6-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="a41a6-173">Notez la manière dont le code utilise uniquement l' `IDataAccess` abstraction :</span><span class="sxs-lookup"><span data-stu-id="a41a6-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="a41a6-174">En interne, la propriété générée ( `DataRepository` ) utilise l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="a41a6-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="a41a6-175">En règle générale, cet attribut n’est pas utilisé directement.</span><span class="sxs-lookup"><span data-stu-id="a41a6-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="a41a6-176">Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base, ajoutez manuellement l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut :</span><span class="sxs-lookup"><span data-stu-id="a41a6-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="a41a6-177">Dans les composants dérivés de la classe de base, la [`@inject`](xref:mvc/views/razor#inject) directive n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="a41a6-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="a41a6-178">Le <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la classe de base est suffisant :</span><span class="sxs-lookup"><span data-stu-id="a41a6-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="a41a6-179">Utiliser DI dans les services</span><span class="sxs-lookup"><span data-stu-id="a41a6-179">Use DI in services</span></span>

<span data-ttu-id="a41a6-180">Les services complexes peuvent nécessiter des services supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="a41a6-180">Complex services might require additional services.</span></span> <span data-ttu-id="a41a6-181">Dans l’exemple précédent, `DataAccess` peut nécessiter le <xref:System.Net.Http.HttpClient> service par défaut.</span><span class="sxs-lookup"><span data-stu-id="a41a6-181">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="a41a6-182">[`@inject`](xref:mvc/views/razor#inject)(ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut) n’est pas disponible pour une utilisation dans les services.</span><span class="sxs-lookup"><span data-stu-id="a41a6-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="a41a6-183">L' *injection de constructeur* doit être utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="a41a6-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="a41a6-184">Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service.</span><span class="sxs-lookup"><span data-stu-id="a41a6-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="a41a6-185">Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.</span><span class="sxs-lookup"><span data-stu-id="a41a6-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="a41a6-186">Dans l’exemple suivant, le constructeur reçoit un <xref:System.Net.Http.HttpClient> via di.</span><span class="sxs-lookup"><span data-stu-id="a41a6-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="a41a6-187"><xref:System.Net.Http.HttpClient>est un service par défaut.</span><span class="sxs-lookup"><span data-stu-id="a41a6-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="a41a6-188">Conditions préalables pour l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="a41a6-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="a41a6-189">Un constructeur doit exister dont les arguments peuvent tous être remplis par DI.</span><span class="sxs-lookup"><span data-stu-id="a41a6-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="a41a6-190">Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="a41a6-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="a41a6-191">Le constructeur applicable doit être `public` .</span><span class="sxs-lookup"><span data-stu-id="a41a6-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="a41a6-192">Un constructeur applicable doit exister.</span><span class="sxs-lookup"><span data-stu-id="a41a6-192">One applicable constructor must exist.</span></span> <span data-ttu-id="a41a6-193">En cas d’ambiguïté, DI lève une exception.</span><span class="sxs-lookup"><span data-stu-id="a41a6-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="a41a6-194">Classes de composants de base de l’utilitaire pour gérer une étendue DI</span><span class="sxs-lookup"><span data-stu-id="a41a6-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="a41a6-195">Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="a41a6-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="a41a6-196">Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI.</span><span class="sxs-lookup"><span data-stu-id="a41a6-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="a41a6-197">Dans Blazor Server les applications, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu.</span><span class="sxs-lookup"><span data-stu-id="a41a6-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="a41a6-198">Dans les Blazor WebAssembly applications, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.</span><span class="sxs-lookup"><span data-stu-id="a41a6-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="a41a6-199">Pour détecter les services transitoires distants dans une application, consultez la section détecter les services temporaires à usage [temporaire](#detect-transient-disposables) .</span><span class="sxs-lookup"><span data-stu-id="a41a6-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="a41a6-200">Une approche qui limite la durée de vie d’un service dans Blazor les applications est l’utilisation du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span><span class="sxs-lookup"><span data-stu-id="a41a6-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="a41a6-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>est un type abstrait dérivé de <xref:Microsoft.AspNetCore.Components.ComponentBase> qui crée une étendue di correspondant à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="a41a6-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="a41a6-202">À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant.</span><span class="sxs-lookup"><span data-stu-id="a41a6-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="a41a6-203">Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés.</span><span class="sxs-lookup"><span data-stu-id="a41a6-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="a41a6-204">Cela peut être utile pour les services qui :</span><span class="sxs-lookup"><span data-stu-id="a41a6-204">This can be useful for services that:</span></span>

* <span data-ttu-id="a41a6-205">Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="a41a6-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="a41a6-206">Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="a41a6-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="a41a6-207">Deux versions du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="a41a6-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="a41a6-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>est un enfant abstrait et jetable du <xref:Microsoft.AspNetCore.Components.ComponentBase> type avec une propriété protégée <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> de type <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="a41a6-209">Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="a41a6-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="a41a6-210">Les services d’injection de services injectés dans le composant à l’aide de [`@inject`](xref:mvc/views/razor#inject) ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut ne sont pas créés dans l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="a41a6-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="a41a6-211">Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> de ou de <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="a41a6-212">Les dépendances de tous les services résolus à l’aide du <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> fournisseur sont fournies à partir de cette même étendue.</span><span class="sxs-lookup"><span data-stu-id="a41a6-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="a41a6-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>dérive de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et ajoute une <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriété qui retourne une instance de `T` à partir du fournisseur di étendu.</span><span class="sxs-lookup"><span data-stu-id="a41a6-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="a41a6-214">Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une instance de <xref:System.IServiceProvider> lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="a41a6-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="a41a6-215">La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="a41a6-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="a41a6-216">Utilisation de l’Entity Framework DbContext à partir de DI</span><span class="sxs-lookup"><span data-stu-id="a41a6-216">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="a41a6-217">Un type de service commun à récupérer à partir de DI dans Web Apps est Entity Framework les objets (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-217">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="a41a6-218">L’inscription des services EF à l’aide de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ajoute en <xref:Microsoft.EntityFrameworkCore.DbContext> tant que service étendu par défaut.</span><span class="sxs-lookup"><span data-stu-id="a41a6-218">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="a41a6-219">L’inscription en tant que service étendu peut entraîner des problèmes dans Blazor les applications, car elle entraîne une <xref:Microsoft.EntityFrameworkCore.DbContext> longue durée de vie des instances et leur partage sur l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="a41a6-219">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="a41a6-220"><xref:Microsoft.EntityFrameworkCore.DbContext>n’est pas thread-safe et ne doit pas être utilisé simultanément.</span><span class="sxs-lookup"><span data-stu-id="a41a6-220"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="a41a6-221">Selon l’application, l’utilisation <xref:Microsoft.AspNetCore.Components.OwningComponentBase> de pour limiter l’étendue d’un <xref:Microsoft.EntityFrameworkCore.DbContext> à un seul composant *peut* résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="a41a6-221">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="a41a6-222">Si un composant n’utilise pas <xref:Microsoft.EntityFrameworkCore.DbContext> en parallèle, le fait de dériver le composant de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et de récupérer le <xref:Microsoft.EntityFrameworkCore.DbContext> de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> est suffisant, car il garantit que :</span><span class="sxs-lookup"><span data-stu-id="a41a6-222">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="a41a6-223">Les composants distincts ne partagent pas un <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-223">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="a41a6-224">La <xref:Microsoft.EntityFrameworkCore.DbContext> fonction vit uniquement tant que le composant en dépend.</span><span class="sxs-lookup"><span data-stu-id="a41a6-224">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="a41a6-225">Si un seul composant peut utiliser un <xref:Microsoft.EntityFrameworkCore.DbContext> en même temps (par exemple, chaque fois qu’un utilisateur sélectionne un bouton), même l’utilisation de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> n’évite pas les problèmes liés à des opérations EF simultanées.</span><span class="sxs-lookup"><span data-stu-id="a41a6-225">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="a41a6-226">Dans ce cas, utilisez un différent <xref:Microsoft.EntityFrameworkCore.DbContext> pour chaque opération EF logique.</span><span class="sxs-lookup"><span data-stu-id="a41a6-226">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="a41a6-227">Utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="a41a6-227">Use either of the following approaches:</span></span>

* <span data-ttu-id="a41a6-228">Créez <xref:Microsoft.EntityFrameworkCore.DbContext> directement à l’aide de <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> comme argument, qui peut être récupéré à partir de di et est thread-safe.</span><span class="sxs-lookup"><span data-stu-id="a41a6-228">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="a41a6-229">Inscrivez le <xref:Microsoft.EntityFrameworkCore.DbContext> dans le conteneur de service avec une durée de vie transitoire :</span><span class="sxs-lookup"><span data-stu-id="a41a6-229">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="a41a6-230">Lors de l’inscription du contexte, utilisez <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-230">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a41a6-231">La <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> méthode d’extension accepte deux paramètres facultatifs de type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-231">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="a41a6-232">Pour utiliser cette approche, seul le `contextLifetime` paramètre doit être <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-232">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a41a6-233">`optionsLifetime`peut conserver sa valeur par défaut <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-233">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="a41a6-234">Le temporaire <xref:Microsoft.EntityFrameworkCore.DbContext> peut être injecté comme normal (à l’aide de [`@inject`](xref:mvc/views/razor#inject) ) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle.</span><span class="sxs-lookup"><span data-stu-id="a41a6-234">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="a41a6-235">Ceux qui peuvent exécuter plusieurs opérations EF simultanément peuvent demander <xref:Microsoft.EntityFrameworkCore.DbContext> des objets distincts pour chaque opération parallèle à l’aide de <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-235">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

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

## <a name="detect-transient-disposables"></a><span data-ttu-id="a41a6-236">Détecter les supprimables temporaires</span><span class="sxs-lookup"><span data-stu-id="a41a6-236">Detect transient disposables</span></span>

<span data-ttu-id="a41a6-237">Les exemples suivants montrent comment détecter des services transitoires jetables dans une application qui doit utiliser <xref:Microsoft.AspNetCore.Components.OwningComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="a41a6-237">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="a41a6-238">Pour plus d’informations, consultez la section [classes du composant de base de l’utilitaire pour gérer une étendue de l’injection de](#utility-base-component-classes-to-manage-a-di-scope) données.</span><span class="sxs-lookup"><span data-stu-id="a41a6-238">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="a41a6-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a41a6-239">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="a41a6-240">Le `TransientDisposable` dans l’exemple suivant est détecté ( `Program.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="a41a6-240">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="a41a6-241">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a41a6-241">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="a41a6-242">`Program`:</span><span class="sxs-lookup"><span data-stu-id="a41a6-242">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="a41a6-243">Le `TransientDependency` dans l’exemple suivant est détecté ( `Startup.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="a41a6-243">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="a41a6-244">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a41a6-244">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="a41a6-245">`IDisposable`conseils pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="a41a6-245">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
