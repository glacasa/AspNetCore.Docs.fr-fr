---
<span data-ttu-id="26fe1-101">titre : « ASP.NET Core Blazor injection de dépendances » auteur : guardrex Description : « Découvrez comment les Blazor applications peuvent injecter des services dans des composants. »</span><span class="sxs-lookup"><span data-stu-id="26fe1-101">title: 'ASP.NET Core Blazor dependency injection' author: guardrex description: 'See how Blazor apps can inject services into components.'</span></span>
<span data-ttu-id="26fe1-102">monikerRange : ' >= aspnetcore-3,1 'ms. Author : Riande ms. Custom : MVC ms. Date : 05/19/2020 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="26fe1-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/19/2020 no-loc:</span></span>
- <span data-ttu-id="26fe1-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="26fe1-103">'Blazor'</span></span>
- <span data-ttu-id="26fe1-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="26fe1-104">'Identity'</span></span>
- <span data-ttu-id="26fe1-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="26fe1-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="26fe1-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="26fe1-106">'Razor'</span></span>
- <span data-ttu-id="26fe1-107">' SignalR 'UID : éblouissant/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="26fe1-107">'SignalR' uid: blazor/dependency-injection</span></span>

---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="26fe1-108">ASP.NET Core l' Blazor injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="26fe1-108">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="26fe1-109">Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="26fe1-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

Blazor<span data-ttu-id="26fe1-110">prend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="26fe1-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="26fe1-111">Les applications peuvent utiliser des services intégrés en les injectant dans des composants.</span><span class="sxs-lookup"><span data-stu-id="26fe1-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="26fe1-112">Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.</span><span class="sxs-lookup"><span data-stu-id="26fe1-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="26fe1-113">La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central.</span><span class="sxs-lookup"><span data-stu-id="26fe1-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="26fe1-114">Cela peut être utile dans les Blazor applications pour :</span><span class="sxs-lookup"><span data-stu-id="26fe1-114">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="26fe1-115">Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .</span><span class="sxs-lookup"><span data-stu-id="26fe1-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="26fe1-116">Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence.</span><span class="sxs-lookup"><span data-stu-id="26fe1-116">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="26fe1-117">Par exemple, considérez une interface `IDataAccess` pour accéder aux données dans l’application.</span><span class="sxs-lookup"><span data-stu-id="26fe1-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="26fe1-118">L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="26fe1-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="26fe1-119">Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret.</span><span class="sxs-lookup"><span data-stu-id="26fe1-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="26fe1-120">L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="26fe1-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="26fe1-121">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="26fe1-121">Default services</span></span>

<span data-ttu-id="26fe1-122">Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="26fe1-122">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="26fe1-123">Service</span><span class="sxs-lookup"><span data-stu-id="26fe1-123">Service</span></span> | <span data-ttu-id="26fe1-124">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="26fe1-124">Lifetime</span></span> | <span data-ttu-id="26fe1-125">Description</span><span class="sxs-lookup"><span data-stu-id="26fe1-125">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="26fe1-126">Temporaire</span><span class="sxs-lookup"><span data-stu-id="26fe1-126">Transient</span></span> | <span data-ttu-id="26fe1-127">Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.</span><span class="sxs-lookup"><span data-stu-id="26fe1-127">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="26fe1-128">L’instance de <xref:System.Net.Http.HttpClient> dans une Blazor application webassembly utilise le navigateur pour gérer le trafic HTTP en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="26fe1-128">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br>Blazor<span data-ttu-id="26fe1-129">Les applications serveur n’incluent pas une <xref:System.Net.Http.HttpClient> configuration en tant que service par défaut.</span><span class="sxs-lookup"><span data-stu-id="26fe1-129"> Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="26fe1-130">Fournissez un <xref:System.Net.Http.HttpClient> à une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="26fe1-130">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="26fe1-131">Pour plus d'informations, consultez <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="26fe1-131">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="26fe1-132">Singleton ( Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="26fe1-132">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="26fe1-133">Étendue ( Blazor serveur)</span><span class="sxs-lookup"><span data-stu-id="26fe1-133">Scoped (Blazor Server)</span></span> | <span data-ttu-id="26fe1-134">Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués.</span><span class="sxs-lookup"><span data-stu-id="26fe1-134">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="26fe1-135">Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="26fe1-135">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="26fe1-136">Singleton ( Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="26fe1-136">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="26fe1-137">Étendue ( Blazor serveur)</span><span class="sxs-lookup"><span data-stu-id="26fe1-137">Scoped (Blazor Server)</span></span> | <span data-ttu-id="26fe1-138">Contient des assistances pour l’utilisation des URI et de l’état de navigation.</span><span class="sxs-lookup"><span data-stu-id="26fe1-138">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="26fe1-139">Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="26fe1-139">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="26fe1-140">Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau.</span><span class="sxs-lookup"><span data-stu-id="26fe1-140">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="26fe1-141">Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.</span><span class="sxs-lookup"><span data-stu-id="26fe1-141">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="26fe1-142">Ajouter des services à une application</span><span class="sxs-lookup"><span data-stu-id="26fe1-142">Add services to an app</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="26fe1-143">Webassembly</span><span class="sxs-lookup"><span data-stu-id="26fe1-143"> WebAssembly</span></span>

<span data-ttu-id="26fe1-144">Configurez les services de la collection de services de l’application dans la `Main` méthode de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="26fe1-144">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="26fe1-145">Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite pour `IMyDependency` :</span><span class="sxs-lookup"><span data-stu-id="26fe1-145">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="26fe1-146">Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="26fe1-146">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="26fe1-147">Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :</span><span class="sxs-lookup"><span data-stu-id="26fe1-147">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="26fe1-148">L’hôte fournit également une instance de configuration centrale pour l’application.</span><span class="sxs-lookup"><span data-stu-id="26fe1-148">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="26fe1-149">En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, *appSettings. JSON*) à `InitializeWeatherAsync` :</span><span class="sxs-lookup"><span data-stu-id="26fe1-149">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="26fe1-150">Serveurs</span><span class="sxs-lookup"><span data-stu-id="26fe1-150"> Server</span></span>

<span data-ttu-id="26fe1-151">Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="26fe1-151">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="26fe1-152"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Une <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , qui est une liste d’objets descripteurs de service (), est passée à la méthode <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-152">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="26fe1-153">Les services sont ajoutés en fournissant des descripteurs de service à la collection de services.</span><span class="sxs-lookup"><span data-stu-id="26fe1-153">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="26fe1-154">L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation concrète `DataAccess` :</span><span class="sxs-lookup"><span data-stu-id="26fe1-154">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="26fe1-155">Durée de vie du service</span><span class="sxs-lookup"><span data-stu-id="26fe1-155">Service lifetime</span></span>

<span data-ttu-id="26fe1-156">Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="26fe1-156">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="26fe1-157">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="26fe1-157">Lifetime</span></span> | <span data-ttu-id="26fe1-158">Description</span><span class="sxs-lookup"><span data-stu-id="26fe1-158">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor<span data-ttu-id="26fe1-159">Les applications webassembly n’ont pas actuellement de concept d’étendues DI.</span><span class="sxs-lookup"><span data-stu-id="26fe1-159"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="26fe1-160">`Scoped`-les services inscrits se comportent comme des `Singleton` services.</span><span class="sxs-lookup"><span data-stu-id="26fe1-160">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="26fe1-161">Toutefois, le Blazor modèle d’hébergement de serveur prend en charge la `Scoped` durée de vie.</span><span class="sxs-lookup"><span data-stu-id="26fe1-161">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="26fe1-162">Dans Blazor les applications serveur, l’inscription d’un service étendu est limitée à la *connexion*.</span><span class="sxs-lookup"><span data-stu-id="26fe1-162">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="26fe1-163">Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="26fe1-163">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="26fe1-164">DI crée une *seule instance* du service.</span><span class="sxs-lookup"><span data-stu-id="26fe1-164">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="26fe1-165">Tous les composants qui requièrent un `Singleton` service reçoivent une instance du même service.</span><span class="sxs-lookup"><span data-stu-id="26fe1-165">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="26fe1-166">Chaque fois qu’un composant obtient une instance d’un `Transient` service à partir du conteneur de service, il reçoit une *nouvelle instance* du service.</span><span class="sxs-lookup"><span data-stu-id="26fe1-166">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="26fe1-167">Le système DI est basé sur le système DI dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26fe1-167">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="26fe1-168">Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="26fe1-168">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="26fe1-169">Demander un service dans un composant</span><span class="sxs-lookup"><span data-stu-id="26fe1-169">Request a service in a component</span></span>

<span data-ttu-id="26fe1-170">Une fois les services ajoutés à la collection de services, injectez les services dans les composants à l’aide de la directive [ \@ Inject](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="26fe1-170">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="26fe1-171">[`@inject`](xref:mvc/views/razor#inject)a deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="26fe1-171">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="26fe1-172">Type : type du service à injecter.</span><span class="sxs-lookup"><span data-stu-id="26fe1-172">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="26fe1-173">Propriété : nom de la propriété qui reçoit le service d’application injecté.</span><span class="sxs-lookup"><span data-stu-id="26fe1-173">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="26fe1-174">La propriété ne nécessite pas de création manuelle.</span><span class="sxs-lookup"><span data-stu-id="26fe1-174">The property doesn't require manual creation.</span></span> <span data-ttu-id="26fe1-175">Le compilateur crée la propriété.</span><span class="sxs-lookup"><span data-stu-id="26fe1-175">The compiler creates the property.</span></span>

<span data-ttu-id="26fe1-176">Pour plus d'informations, consultez <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="26fe1-176">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="26fe1-177">Utilisez plusieurs [`@inject`](xref:mvc/views/razor#inject) instructions pour injecter différents services.</span><span class="sxs-lookup"><span data-stu-id="26fe1-177">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="26fe1-178">L’exemple suivant montre comment utiliser [`@inject`](xref:mvc/views/razor#inject) .</span><span class="sxs-lookup"><span data-stu-id="26fe1-178">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="26fe1-179">Le service qui implémente `Services.IDataAccess` est injecté dans la propriété du composant `DataRepository` .</span><span class="sxs-lookup"><span data-stu-id="26fe1-179">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="26fe1-180">Notez la manière dont le code utilise uniquement l' `IDataAccess` abstraction :</span><span class="sxs-lookup"><span data-stu-id="26fe1-180">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="26fe1-181">En interne, la propriété générée ( `DataRepository` ) utilise l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="26fe1-181">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="26fe1-182">En règle générale, cet attribut n’est pas utilisé directement.</span><span class="sxs-lookup"><span data-stu-id="26fe1-182">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="26fe1-183">Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base, ajoutez manuellement l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut :</span><span class="sxs-lookup"><span data-stu-id="26fe1-183">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="26fe1-184">Dans les composants dérivés de la classe de base, la [`@inject`](xref:mvc/views/razor#inject) directive n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="26fe1-184">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="26fe1-185">Le <xref:Microsoft.AspNetCore.Components.InjectAttribute> de la classe de base est suffisant :</span><span class="sxs-lookup"><span data-stu-id="26fe1-185">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="26fe1-186">Utiliser DI dans les services</span><span class="sxs-lookup"><span data-stu-id="26fe1-186">Use DI in services</span></span>

<span data-ttu-id="26fe1-187">Les services complexes peuvent nécessiter des services supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="26fe1-187">Complex services might require additional services.</span></span> <span data-ttu-id="26fe1-188">Dans l’exemple précédent, `DataAccess` peut nécessiter le <xref:System.Net.Http.HttpClient> service par défaut.</span><span class="sxs-lookup"><span data-stu-id="26fe1-188">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="26fe1-189">[`@inject`](xref:mvc/views/razor#inject)(ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut) n’est pas disponible pour une utilisation dans les services.</span><span class="sxs-lookup"><span data-stu-id="26fe1-189">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="26fe1-190">L' *injection de constructeur* doit être utilisée à la place.</span><span class="sxs-lookup"><span data-stu-id="26fe1-190">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="26fe1-191">Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service.</span><span class="sxs-lookup"><span data-stu-id="26fe1-191">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="26fe1-192">Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.</span><span class="sxs-lookup"><span data-stu-id="26fe1-192">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="26fe1-193">Conditions préalables pour l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="26fe1-193">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="26fe1-194">Un constructeur doit exister dont les arguments peuvent tous être remplis par DI.</span><span class="sxs-lookup"><span data-stu-id="26fe1-194">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="26fe1-195">Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="26fe1-195">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="26fe1-196">Le constructeur applicable doit être *public*.</span><span class="sxs-lookup"><span data-stu-id="26fe1-196">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="26fe1-197">Un constructeur applicable doit exister.</span><span class="sxs-lookup"><span data-stu-id="26fe1-197">One applicable constructor must exist.</span></span> <span data-ttu-id="26fe1-198">En cas d’ambiguïté, DI lève une exception.</span><span class="sxs-lookup"><span data-stu-id="26fe1-198">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="26fe1-199">Classes de composants de base de l’utilitaire pour gérer une étendue DI</span><span class="sxs-lookup"><span data-stu-id="26fe1-199">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="26fe1-200">Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="26fe1-200">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="26fe1-201">Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI.</span><span class="sxs-lookup"><span data-stu-id="26fe1-201">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="26fe1-202">Dans Blazor les applications serveur, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu.</span><span class="sxs-lookup"><span data-stu-id="26fe1-202">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="26fe1-203">Dans les Blazor applications Webassembly, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.</span><span class="sxs-lookup"><span data-stu-id="26fe1-203">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="26fe1-204">Une approche qui limite la durée de vie d’un service dans Blazor les applications est l’utilisation du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span><span class="sxs-lookup"><span data-stu-id="26fe1-204">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="26fe1-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>est un type abstrait dérivé de <xref:Microsoft.AspNetCore.Components.ComponentBase> qui crée une étendue di correspondant à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="26fe1-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="26fe1-206">À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant.</span><span class="sxs-lookup"><span data-stu-id="26fe1-206">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="26fe1-207">Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés.</span><span class="sxs-lookup"><span data-stu-id="26fe1-207">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="26fe1-208">Cela peut être utile pour les services qui :</span><span class="sxs-lookup"><span data-stu-id="26fe1-208">This can be useful for services that:</span></span>

* <span data-ttu-id="26fe1-209">Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="26fe1-209">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="26fe1-210">Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.</span><span class="sxs-lookup"><span data-stu-id="26fe1-210">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="26fe1-211">Deux versions du <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="26fe1-211">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="26fe1-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>est un enfant abstrait et jetable du <xref:Microsoft.AspNetCore.Components.ComponentBase> type avec une propriété protégée <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> de type <xref:System.IServiceProvider> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="26fe1-213">Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.</span><span class="sxs-lookup"><span data-stu-id="26fe1-213">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="26fe1-214">Les services d’injection de services injectés dans le composant à l’aide de [`@inject`](xref:mvc/views/razor#inject) ou l' [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribut ne sont pas créés dans l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="26fe1-214">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="26fe1-215">Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> de ou de <xref:System.IServiceProvider.GetService%2A> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-215">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="26fe1-216">Les dépendances de tous les services résolus à l’aide du <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> fournisseur sont fournies à partir de cette même étendue.</span><span class="sxs-lookup"><span data-stu-id="26fe1-216">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="26fe1-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>dérive de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et ajoute une <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> propriété qui retourne une instance de `T` à partir du fournisseur di étendu.</span><span class="sxs-lookup"><span data-stu-id="26fe1-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="26fe1-218">Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une instance de <xref:System.IServiceProvider> lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant.</span><span class="sxs-lookup"><span data-stu-id="26fe1-218">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="26fe1-219">La <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="26fe1-219">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="26fe1-220">Utilisation de l’Entity Framework DbContext à partir de DI</span><span class="sxs-lookup"><span data-stu-id="26fe1-220">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="26fe1-221">Un type de service commun à récupérer à partir de DI dans Web Apps est Entity Framework les objets (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-221">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span> <span data-ttu-id="26fe1-222">L’inscription des services EF à l’aide de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ajoute en <xref:Microsoft.EntityFrameworkCore.DbContext> tant que service étendu par défaut.</span><span class="sxs-lookup"><span data-stu-id="26fe1-222">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span> <span data-ttu-id="26fe1-223">L’inscription en tant que service étendu peut entraîner des problèmes dans Blazor les applications, car elle entraîne une <xref:Microsoft.EntityFrameworkCore.DbContext> longue durée de vie des instances et leur partage sur l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="26fe1-223">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="26fe1-224"><xref:Microsoft.EntityFrameworkCore.DbContext>n’est pas thread-safe et ne doit pas être utilisé simultanément.</span><span class="sxs-lookup"><span data-stu-id="26fe1-224"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="26fe1-225">Selon l’application, l’utilisation <xref:Microsoft.AspNetCore.Components.OwningComponentBase> de pour limiter l’étendue d’un <xref:Microsoft.EntityFrameworkCore.DbContext> à un seul composant *peut* résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="26fe1-225">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="26fe1-226">Si un composant n’utilise pas <xref:Microsoft.EntityFrameworkCore.DbContext> en parallèle, le fait de dériver le composant de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> et de récupérer le <xref:Microsoft.EntityFrameworkCore.DbContext> de <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> est suffisant, car il garantit que :</span><span class="sxs-lookup"><span data-stu-id="26fe1-226">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="26fe1-227">Les composants distincts ne partagent pas un <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-227">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
* <span data-ttu-id="26fe1-228">La <xref:Microsoft.EntityFrameworkCore.DbContext> fonction vit uniquement tant que le composant en dépend.</span><span class="sxs-lookup"><span data-stu-id="26fe1-228">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>

<span data-ttu-id="26fe1-229">Si un seul composant peut utiliser un <xref:Microsoft.EntityFrameworkCore.DbContext> en même temps (par exemple, chaque fois qu’un utilisateur sélectionne un bouton), même l’utilisation de <xref:Microsoft.AspNetCore.Components.OwningComponentBase> n’évite pas les problèmes liés à des opérations EF simultanées.</span><span class="sxs-lookup"><span data-stu-id="26fe1-229">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="26fe1-230">Dans ce cas, utilisez un différent <xref:Microsoft.EntityFrameworkCore.DbContext> pour chaque opération EF logique.</span><span class="sxs-lookup"><span data-stu-id="26fe1-230">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="26fe1-231">Utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="26fe1-231">Use either of the following approaches:</span></span>

* <span data-ttu-id="26fe1-232">Créez <xref:Microsoft.EntityFrameworkCore.DbContext> directement à l’aide de <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> comme argument, qui peut être récupéré à partir de di et est thread-safe.</span><span class="sxs-lookup"><span data-stu-id="26fe1-232">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

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

* <span data-ttu-id="26fe1-233">Inscrivez le <xref:Microsoft.EntityFrameworkCore.DbContext> dans le conteneur de service avec une durée de vie transitoire :</span><span class="sxs-lookup"><span data-stu-id="26fe1-233">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="26fe1-234">Lors de l’inscription du contexte, utilisez <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-234">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="26fe1-235">La <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> méthode d’extension accepte deux paramètres facultatifs de type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-235">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span> <span data-ttu-id="26fe1-236">Pour utiliser cette approche, seul le `contextLifetime` paramètre doit être <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-236">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span> <span data-ttu-id="26fe1-237">`optionsLifetime`peut conserver sa valeur par défaut <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-237">`optionsLifetime` can keep its default value of <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType>.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="26fe1-238">Le temporaire <xref:Microsoft.EntityFrameworkCore.DbContext> peut être injecté comme normal (à l’aide de [`@inject`](xref:mvc/views/razor#inject) ) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle.</span><span class="sxs-lookup"><span data-stu-id="26fe1-238">The transient <xref:Microsoft.EntityFrameworkCore.DbContext> can be injected as normal (using [`@inject`](xref:mvc/views/razor#inject)) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="26fe1-239">Ceux qui peuvent exécuter plusieurs opérations EF simultanément peuvent demander <xref:Microsoft.EntityFrameworkCore.DbContext> des objets distincts pour chaque opération parallèle à l’aide de <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .</span><span class="sxs-lookup"><span data-stu-id="26fe1-239">Those that may perform multiple EF operations simultaneously can request separate <xref:Microsoft.EntityFrameworkCore.DbContext> objects for each parallel operation using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A>.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="26fe1-240">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="26fe1-240">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="26fe1-241">Recommandations IDisposable pour les instances temporaires et partagées</span><span class="sxs-lookup"><span data-stu-id="26fe1-241">IDisposable guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
