---
<span data-ttu-id="03619-101">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-101">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-103">'Blazor'</span></span>
- <span data-ttu-id="03619-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-104">'Identity'</span></span>
- <span data-ttu-id="03619-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-106">'Razor'</span></span>
- <span data-ttu-id="03619-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="03619-108">ASP.NET Core l' Blazor injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="03619-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="03619-109">Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="03619-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="03619-110">prend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="03619-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="03619-111">Les applications peuvent utiliser des services intégrés en les injectant dans des composants.</span><span class="sxs-lookup"><span data-stu-id="03619-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="03619-112">Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.</span><span class="sxs-lookup"><span data-stu-id="03619-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="03619-113">La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central.</span><span class="sxs-lookup"><span data-stu-id="03619-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="03619-114">Cela peut être utile dans les Blazor applications pour :</span><span class="sxs-lookup"><span data-stu-id="03619-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="03619-115">Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .</span><span class="sxs-lookup"><span data-stu-id="03619-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="03619-116">Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence.</span><span class="sxs-lookup"><span data-stu-id="03619-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="03619-117">Par exemple, considérez une interface `IDataAccess` pour accéder aux données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="03619-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="03619-118">L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="03619-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="03619-119">Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret.</span><span class="sxs-lookup"><span data-stu-id="03619-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="03619-120">L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="03619-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="03619-121">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="03619-121">Default services</span></span>

<span data-ttu-id="03619-122">Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="03619-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="03619-123">Service</span><span class="sxs-lookup"><span data-stu-id="03619-123">Service</span></span> | <span data-ttu-id="03619-124">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="03619-124">Lifetime</span></span> | <span data-ttu-id="03619-125">Description</span><span class="sxs-lookup"><span data-stu-id="03619-125">Description</span></span> |
| ---
<span data-ttu-id="03619-126">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-126">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-127">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-127">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-128">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-128">'Blazor'</span></span>
- <span data-ttu-id="03619-129">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-129">'Identity'</span></span>
- <span data-ttu-id="03619-130">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-130">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-131">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-131">'Razor'</span></span>
- <span data-ttu-id="03619-132">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-132">'SignalR' uid:</span></span> 

<span data-ttu-id="03619-133">---- | titre de la--- : 'ASP.NET Core Blazor injection de dépendances’auteur : Description : 'Découvrez comment les Blazor applications peuvent injecter des services dans des composants.</span><span class="sxs-lookup"><span data-stu-id="03619-133">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-134">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-134">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-135">'Blazor'</span></span>
- <span data-ttu-id="03619-136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-136">'Identity'</span></span>
- <span data-ttu-id="03619-137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-137">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-138">'Razor'</span></span>
- <span data-ttu-id="03619-139">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03619-140">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-140">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-141">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-141">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-142">'Blazor'</span></span>
- <span data-ttu-id="03619-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-143">'Identity'</span></span>
- <span data-ttu-id="03619-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-145">'Razor'</span></span>
- <span data-ttu-id="03619-146">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-146">'SignalR' uid:</span></span> 

<span data-ttu-id="03619-147">---- | titre de la--- : 'ASP.NET Core Blazor injection de dépendances’auteur : Description : 'Découvrez comment les Blazor applications peuvent injecter des services dans des composants.</span><span class="sxs-lookup"><span data-stu-id="03619-147">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-148">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-148">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-149">'Blazor'</span></span>
- <span data-ttu-id="03619-150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-150">'Identity'</span></span>
- <span data-ttu-id="03619-151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-151">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-152">'Razor'</span></span>
- <span data-ttu-id="03619-153">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03619-154">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-154">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-155">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-155">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-156">'Blazor'</span></span>
- <span data-ttu-id="03619-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-157">'Identity'</span></span>
- <span data-ttu-id="03619-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-159">'Razor'</span></span>
- <span data-ttu-id="03619-160">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03619-161">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-161">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-162">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-162">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-163">'Blazor'</span></span>
- <span data-ttu-id="03619-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-164">'Identity'</span></span>
- <span data-ttu-id="03619-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-166">'Razor'</span></span>
- <span data-ttu-id="03619-167">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-167">'SignalR' uid:</span></span> 

<span data-ttu-id="03619-168">------ | | <xref:System.Net.Http.HttpClient> | Temporaire | Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.</span><span class="sxs-lookup"><span data-stu-id="03619-168">------ | | <xref:System.Net.Http.HttpClient> | Transient | Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="03619-169">L’instance de `HttpClient` dans une Blazor application webassembly utilise le navigateur pour gérer le trafic HTTP en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="03619-169">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="03619-170">Les applications serveur n’incluent pas une `HttpClient` configuration en tant que service par défaut.</span><span class="sxs-lookup"><span data-stu-id="03619-170"> Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="03619-171">Fournissez un `HttpClient` à une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="03619-171">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="03619-172">Pour plus d'informations, consultez <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="03619-172">For more information, see <xref:blazor/call-web-api>.</span></span> <span data-ttu-id="03619-173">| | `IJSRuntime` | Singleton ( Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="03619-173">| | `IJSRuntime` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="03619-174">Étendu ( Blazor serveur) | Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués.</span><span class="sxs-lookup"><span data-stu-id="03619-174">Scoped (Blazor Server) | Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="03619-175">Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="03619-175">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="03619-176">| | `NavigationManager` | Singleton ( Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="03619-176">| | `NavigationManager` | Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="03619-177">Étendu ( Blazor serveur) | Contient des assistances pour l’utilisation des URI et de l’état de navigation.</span><span class="sxs-lookup"><span data-stu-id="03619-177">Scoped (Blazor Server) | Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="03619-178">Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="03619-178">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="03619-179">Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="03619-179">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="03619-180">Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="03619-180">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="03619-181">Ajouter des services à une application</span><span class="sxs-lookup"><span data-stu-id="03619-181">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="03619-182">Webassembly</span><span class="sxs-lookup"><span data-stu-id="03619-182"> WebAssembly</span></span>

<span data-ttu-id="03619-183">Configurez les services de la collection de services de l’application dans la `Main` méthode de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="03619-183">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="03619-184">Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite pour `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="03619-184">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="03619-185">Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="03619-185">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="03619-186">Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :</span><span class="sxs-lookup"><span data-stu-id="03619-186">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="03619-187">L’hôte fournit également une instance de configuration centrale pour l’application.</span><span class="sxs-lookup"><span data-stu-id="03619-187">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="03619-188">En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, *appSettings. JSON*) à `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="03619-188">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="03619-189">Serveurs</span><span class="sxs-lookup"><span data-stu-id="03619-189"> Server</span></span>

<span data-ttu-id="03619-190">Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="03619-190">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="03619-191">`ConfigureServices`Une <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , qui est une liste d’objets descripteurs de service (), est passée à la méthode <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="03619-191">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="03619-192">Les services sont ajoutés en fournissant des descripteurs de service à la collection de services.</span><span class="sxs-lookup"><span data-stu-id="03619-192">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="03619-193">L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation concrète `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="03619-193">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="03619-194">Durée de vie du service</span><span class="sxs-lookup"><span data-stu-id="03619-194">Service lifetime</span></span>

<span data-ttu-id="03619-195">Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="03619-195">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="03619-196">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="03619-196">Lifetime</span></span> | <span data-ttu-id="03619-197">Description</span><span class="sxs-lookup"><span data-stu-id="03619-197">Description</span></span> |
| ---
<span data-ttu-id="03619-198">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-198">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-199">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-199">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-200">'Blazor'</span></span>
- <span data-ttu-id="03619-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-201">'Identity'</span></span>
- <span data-ttu-id="03619-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-203">'Razor'</span></span>
- <span data-ttu-id="03619-204">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03619-205">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-205">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-206">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-206">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-207">'Blazor'</span></span>
- <span data-ttu-id="03619-208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-208">'Identity'</span></span>
- <span data-ttu-id="03619-209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-209">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-210">'Razor'</span></span>
- <span data-ttu-id="03619-211">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-211">'SignalR' uid:</span></span> 

<span data-ttu-id="03619-212">---- | titre de la--- : 'ASP.NET Core Blazor injection de dépendances’auteur : Description : 'Découvrez comment les Blazor applications peuvent injecter des services dans des composants.</span><span class="sxs-lookup"><span data-stu-id="03619-212">---- | --- title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-213">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-213">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-214">'Blazor'</span></span>
- <span data-ttu-id="03619-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-215">'Identity'</span></span>
- <span data-ttu-id="03619-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-217">'Razor'</span></span>
- <span data-ttu-id="03619-218">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03619-219">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-219">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-220">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-220">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-221">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-221">'Blazor'</span></span>
- <span data-ttu-id="03619-222">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-222">'Identity'</span></span>
- <span data-ttu-id="03619-223">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-223">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-224">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-224">'Razor'</span></span>
- <span data-ttu-id="03619-225">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-225">'SignalR' uid:</span></span> 

-
<span data-ttu-id="03619-226">titre : « ASP.NET Core Blazor injection de dépendances » auteur : Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="03619-226">title: 'ASP.NET Core Blazor dependency injection' author: description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="03619-227">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="03619-227">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="03619-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="03619-228">'Blazor'</span></span>
- <span data-ttu-id="03619-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="03619-229">'Identity'</span></span>
- <span data-ttu-id="03619-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="03619-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="03619-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="03619-231">'Razor'</span></span>
- <span data-ttu-id="03619-232">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="03619-232">'SignalR' uid:</span></span> 

<span data-ttu-id="03619-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor Les applications webassembly n’ont pas actuellement de concept d’étendues DI.</span><span class="sxs-lookup"><span data-stu-id="03619-233">------ | | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="03619-234">`Scoped`-les services inscrits se comportent comme des `Singleton` services.</span><span class="sxs-lookup"><span data-stu-id="03619-234">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="03619-235">Toutefois, le Blazor modèle d’hébergement de serveur prend en charge la `Scoped` durée de vie.</span><span class="sxs-lookup"><span data-stu-id="03619-235">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="03619-236">Dans Blazor les applications serveur, l’inscription d’un service étendu est limitée à la *connexion*.</span><span class="sxs-lookup"><span data-stu-id="03619-236">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="03619-237">Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="03619-237">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> <span data-ttu-id="03619-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI crée une *seule instance* du service.</span><span class="sxs-lookup"><span data-stu-id="03619-238">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI creates a *single instance* of the service.</span></span> <span data-ttu-id="03619-239">Tous les composants qui requièrent un `Singleton` service reçoivent une instance du même service.</span><span class="sxs-lookup"><span data-stu-id="03619-239">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="03619-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Chaque fois qu’un composant obtient une instance d’un `Transient` service à partir du conteneur de service, il reçoit une *nouvelle instance* du service.</span><span class="sxs-lookup"><span data-stu-id="03619-240">| | <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="03619-241">Le système DI est basé sur le système DI dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03619-241">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="03619-242">Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="03619-242">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="03619-243">Demander un service dans un composant</span><span class="sxs-lookup"><span data-stu-id="03619-243">Request a service in a component</span></span>

<span data-ttu-id="03619-244">Une fois les services ajoutés à la collection de services, injectez les services dans les composants à l’aide de la directive [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="03619-244">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="03619-245">`@inject`a deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="03619-245">`@inject` has two parameters:</span></span>

* <span data-ttu-id="03619-246">Tapez &ndash; le type du service à injecter.</span><span class="sxs-lookup"><span data-stu-id="03619-246">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="03619-247">Propriété &ndash; nom de la propriété qui reçoit le service d’application injecté.</span><span class="sxs-lookup"><span data-stu-id="03619-247">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="03619-248">La propriété ne nécessite pas de création manuelle.</span><span class="sxs-lookup"><span data-stu-id="03619-248">The property doesn't require manual creation.</span></span> <span data-ttu-id="03619-249">Le compilateur crée la propriété.</span><span class="sxs-lookup"><span data-stu-id="03619-249">The compiler creates the property.</span></span>

<span data-ttu-id="03619-250">Pour plus d'informations, consultez <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="03619-250">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="03619-251">Utilisez plusieurs `@inject` instructions pour injecter différents services.</span><span class="sxs-lookup"><span data-stu-id="03619-251">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="03619-252">L'exemple suivant montre comment utiliser `@inject`.</span><span class="sxs-lookup"><span data-stu-id="03619-252">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="03619-253">Le service qui implémente `Services.IDataAccess` est injecté dans la propriété du composant `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="03619-253">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="03619-254">Notez la manière dont le code utilise uniquement l' `IDataAccess` abstraction :</span><span class="sxs-lookup"><span data-stu-id="03619-254">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="03619-255">En interne, la propriété générée ( `DataRepository` ) utilise l' `InjectAttribute` attribut.</span><span class="sxs-lookup"><span data-stu-id="03619-255">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="03619-256">En règle générale, cet attribut n’est pas utilisé directement.</span><span class="sxs-lookup"><span data-stu-id="03619-256">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="03619-257">Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base, ajoutez manuellement le `InjectAttribute` :</span><span class="sxs-lookup"><span data-stu-id="03619-257">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="03619-258">Dans les composants dérivés de la classe de base, la `@inject` directive n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="03619-258">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="03619-259">Le `InjectAttribute` de la classe de base est suffisant :</span><span class="sxs-lookup"><span data-stu-id="03619-259">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="03619-260">Utiliser DI dans les services</span><span class="sxs-lookup"><span data-stu-id="03619-260">Use DI in services</span></span>

<span data-ttu-id="03619-261">Les services complexes peuvent nécessiter des services supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="03619-261">Complex services might require additional services.</span></span> <span data-ttu-id="03619-262">Dans l’exemple précédent, `DataAccess` peut nécessiter le `HttpClient` service par défaut.</span><span class="sxs-lookup"><span data-stu-id="03619-262">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="03619-263">`@inject`(ou `InjectAttribute` ) n’est pas disponible pour une utilisation dans les services.</span><span class="sxs-lookup"><span data-stu-id="03619-263">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="03619-264">L' *injection de constructeur* doit être utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="03619-264">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="03619-265">Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service.</span><span class="sxs-lookup"><span data-stu-id="03619-265">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="03619-266">Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.</span><span class="sxs-lookup"><span data-stu-id="03619-266">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="03619-267">Conditions préalables pour l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="03619-267">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="03619-268">Un constructeur doit exister dont les arguments peuvent tous être remplis par DI.</span><span class="sxs-lookup"><span data-stu-id="03619-268">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="03619-269">Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="03619-269">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="03619-270">Le constructeur applicable doit être *public*.</span><span class="sxs-lookup"><span data-stu-id="03619-270">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="03619-271">Un constructeur applicable doit exister.</span><span class="sxs-lookup"><span data-stu-id="03619-271">One applicable constructor must exist.</span></span> <span data-ttu-id="03619-272">En cas d’ambiguïté, DI lève une exception.</span><span class="sxs-lookup"><span data-stu-id="03619-272">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="03619-273">Classes de composants de base de l’utilitaire pour gérer une étendue DI</span><span class="sxs-lookup"><span data-stu-id="03619-273">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="03619-274">Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="03619-274">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="03619-275">Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI.</span><span class="sxs-lookup"><span data-stu-id="03619-275">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="03619-276">Dans Blazor les applications serveur, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu.</span><span class="sxs-lookup"><span data-stu-id="03619-276">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="03619-277">Dans les Blazor applications Webassembly, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.</span><span class="sxs-lookup"><span data-stu-id="03619-277">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="03619-278">Une approche qui limite la durée de vie d’un service dans Blazor les applications est l’utilisation du `OwningComponentBase` type.</span><span class="sxs-lookup"><span data-stu-id="03619-278">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="03619-279">`OwningComponentBase`est un type abstrait dérivé de `ComponentBase` qui crée une étendue di correspondant à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="03619-279">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="03619-280">À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant.</span><span class="sxs-lookup"><span data-stu-id="03619-280">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="03619-281">Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés.</span><span class="sxs-lookup"><span data-stu-id="03619-281">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="03619-282">Cela peut être utile pour les services qui :</span><span class="sxs-lookup"><span data-stu-id="03619-282">This can be useful for services that:</span></span>

* <span data-ttu-id="03619-283">Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="03619-283">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="03619-284">Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="03619-284">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="03619-285">Deux versions du `OwningComponentBase` type sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="03619-285">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="03619-286">`OwningComponentBase`est un enfant abstrait et jetable du `ComponentBase` type avec une propriété protégée `ScopedServices` de type `IServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="03619-286">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="03619-287">Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="03619-287">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="03619-288">Les services d’injection de services injectés dans le composant à l’aide `@inject` de ou `InjectAttribute` ( `[Inject]` ) ne sont pas créés dans l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="03619-288">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="03619-289">Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide `ScopedServices.GetRequiredService` de ou de `ScopedServices.GetService` .</span><span class="sxs-lookup"><span data-stu-id="03619-289">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="03619-290">Les dépendances de tous les services résolus à l’aide du `ScopedServices` fournisseur sont fournies à partir de cette même étendue.</span><span class="sxs-lookup"><span data-stu-id="03619-290">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="03619-291">`OwningComponentBase<T>`dérive de `OwningComponentBase` et ajoute une propriété `Service` qui retourne une instance de `T` à partir du fournisseur di étendu.</span><span class="sxs-lookup"><span data-stu-id="03619-291">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="03619-292">Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une instance de `IServiceProvider` lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="03619-292">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="03619-293">La `ScopedServices` propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="03619-293">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="03619-294">Utilisation de l’Entity Framework DbContext à partir de DI</span><span class="sxs-lookup"><span data-stu-id="03619-294">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="03619-295">Un type de service commun à récupérer à partir de DI dans Web Apps est Entity Framework les objets (EF) `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="03619-295">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="03619-296">L’inscription des services EF à l’aide de `IServiceCollection.AddDbContext` ajoute en `DbContext` tant que service étendu par défaut.</span><span class="sxs-lookup"><span data-stu-id="03619-296">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="03619-297">L’inscription en tant que service étendu peut entraîner des problèmes dans Blazor les applications, car elle entraîne une `DbContext` longue durée de vie des instances et leur partage sur l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="03619-297">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="03619-298">`DbContext`n’est pas thread-safe et ne doit pas être utilisé simultanément.</span><span class="sxs-lookup"><span data-stu-id="03619-298">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="03619-299">Selon l’application, l’utilisation `OwningComponentBase` de pour limiter l’étendue d’un `DbContext` à un seul composant *peut* résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="03619-299">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="03619-300">Si un composant n’utilise pas `DbContext` en parallèle, le fait de dériver le composant de `OwningComponentBase` et de récupérer le `DbContext` de `ScopedServices` est suffisant, car il garantit que :</span><span class="sxs-lookup"><span data-stu-id="03619-300">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="03619-301">Les composants distincts ne partagent pas un `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="03619-301">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="03619-302">La `DbContext` fonction vit uniquement tant que le composant en dépend.</span><span class="sxs-lookup"><span data-stu-id="03619-302">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="03619-303">Si un seul composant peut utiliser un `DbContext` en même temps (par exemple, chaque fois qu’un utilisateur sélectionne un bouton), même l’utilisation de `OwningComponentBase` n’évite pas les problèmes liés à des opérations EF simultanées.</span><span class="sxs-lookup"><span data-stu-id="03619-303">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="03619-304">Dans ce cas, utilisez un différent `DbContext` pour chaque opération EF logique.</span><span class="sxs-lookup"><span data-stu-id="03619-304">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="03619-305">Utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="03619-305">Use either of the following approaches:</span></span>

* <span data-ttu-id="03619-306">Créez `DbContext` directement à l’aide de `DbContextOptions<TContext>` comme argument, qui peut être récupéré à partir de di et est thread-safe.</span><span class="sxs-lookup"><span data-stu-id="03619-306">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="03619-307">Inscrivez le `DbContext` dans le conteneur de service avec une durée de vie transitoire :</span><span class="sxs-lookup"><span data-stu-id="03619-307">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="03619-308">Lors de l’inscription du contexte, utilisez `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="03619-308">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="03619-309">La `AddDbContext` méthode d’extension accepte deux paramètres facultatifs de type `ServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="03619-309">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="03619-310">Pour utiliser cette approche, seul le `contextLifetime` paramètre doit être `ServiceLifetime.Transient` .</span><span class="sxs-lookup"><span data-stu-id="03619-310">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="03619-311">`optionsLifetime`peut conserver sa valeur par défaut `ServiceLifetime.Scoped` .</span><span class="sxs-lookup"><span data-stu-id="03619-311">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="03619-312">Le temporaire `DbContext` peut être injecté comme normal (à l’aide de `@inject` ) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle.</span><span class="sxs-lookup"><span data-stu-id="03619-312">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="03619-313">Ceux qui peuvent exécuter plusieurs opérations EF simultanément peuvent demander `DbContext` des objets distincts pour chaque opération parallèle à l’aide de `IServiceProvider.GetRequiredService` .</span><span class="sxs-lookup"><span data-stu-id="03619-313">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="03619-314">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="03619-314">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
