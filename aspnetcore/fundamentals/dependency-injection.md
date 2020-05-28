---
<span data-ttu-id="68b1c-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-102">'Blazor'</span></span>
- <span data-ttu-id="68b1c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-103">'Identity'</span></span>
- <span data-ttu-id="68b1c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-105">'Razor'</span></span>
- <span data-ttu-id="68b1c-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="68b1c-107">Injection de dépendances dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68b1c-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="68b1c-108">Par [Steve Smith](https://ardalis.com/) et [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="68b1c-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68b1c-109">ASP.NET Core prend en charge le modèle de conception de logiciel avec injection de dépendances (DI), une technique permettant d’obtenir une [inversion de contrôle (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="68b1c-110">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="68b1c-111">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68b1c-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="68b1c-112">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="68b1c-112">Overview of dependency injection</span></span>

<span data-ttu-id="68b1c-113">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="68b1c-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="68b1c-114">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="68b1c-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="68b1c-115">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="68b1c-116">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="68b1c-117">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="68b1c-118">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="68b1c-119">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="68b1c-120">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="68b1c-121">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="68b1c-122">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="68b1c-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="68b1c-123">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="68b1c-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="68b1c-124">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="68b1c-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="68b1c-125">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="68b1c-126">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="68b1c-127">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="68b1c-128">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="68b1c-129">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="68b1c-130">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="68b1c-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="68b1c-131">Dans l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="68b1c-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="68b1c-132">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="68b1c-133">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="68b1c-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="68b1c-134">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="68b1c-135">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="68b1c-136">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="68b1c-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="68b1c-137">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="68b1c-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="68b1c-138">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="68b1c-139">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="68b1c-140">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="68b1c-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="68b1c-141">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="68b1c-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="68b1c-142">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="68b1c-143">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="68b1c-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="68b1c-144">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="68b1c-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="68b1c-145">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="68b1c-146">Par exemple, `services.AddMvc()` ajoute les pages de services Razor et MVC requièrent.</span><span class="sxs-lookup"><span data-stu-id="68b1c-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="68b1c-147">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="68b1c-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="68b1c-148">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="68b1c-149">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="68b1c-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="68b1c-150">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="68b1c-151">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="68b1c-152">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="68b1c-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="68b1c-153">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="68b1c-153">Services injected into Startup</span></span>

<span data-ttu-id="68b1c-154">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="68b1c-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="68b1c-155">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="68b1c-156">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="68b1c-157">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="68b1c-157">Framework-provided services</span></span>

<span data-ttu-id="68b1c-158">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="68b1c-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="68b1c-159">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="68b1c-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="68b1c-160">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="68b1c-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="68b1c-161">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="68b1c-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="68b1c-162">Type de service</span><span class="sxs-lookup"><span data-stu-id="68b1c-162">Service Type</span></span> | <span data-ttu-id="68b1c-163">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="68b1c-163">Lifetime</span></span> |
| ---
<span data-ttu-id="68b1c-164">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-165">'Blazor'</span></span>
- <span data-ttu-id="68b1c-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-166">'Identity'</span></span>
- <span data-ttu-id="68b1c-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-168">'Razor'</span></span>
- <span data-ttu-id="68b1c-169">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-170">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-171">'Blazor'</span></span>
- <span data-ttu-id="68b1c-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-172">'Identity'</span></span>
- <span data-ttu-id="68b1c-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-174">'Razor'</span></span>
- <span data-ttu-id="68b1c-175">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-176">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-177">'Blazor'</span></span>
- <span data-ttu-id="68b1c-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-178">'Identity'</span></span>
- <span data-ttu-id="68b1c-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-180">'Razor'</span></span>
- <span data-ttu-id="68b1c-181">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-182">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-183">'Blazor'</span></span>
- <span data-ttu-id="68b1c-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-184">'Identity'</span></span>
- <span data-ttu-id="68b1c-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-186">'Razor'</span></span>
- <span data-ttu-id="68b1c-187">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-187">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-188">------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-189">'Blazor'</span></span>
- <span data-ttu-id="68b1c-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-190">'Identity'</span></span>
- <span data-ttu-id="68b1c-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-192">'Razor'</span></span>
- <span data-ttu-id="68b1c-193">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-194">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-195">'Blazor'</span></span>
- <span data-ttu-id="68b1c-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-196">'Identity'</span></span>
- <span data-ttu-id="68b1c-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-198">'Razor'</span></span>
- <span data-ttu-id="68b1c-199">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-199">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaire | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaire | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaire | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaire | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="68b1c-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="68b1c-201">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="68b1c-201">Register additional services with extension methods</span></span>

<span data-ttu-id="68b1c-202">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="68b1c-203">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="68b1c-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="68b1c-204">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="68b1c-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="68b1c-205">Durées de service</span><span class="sxs-lookup"><span data-stu-id="68b1c-205">Service lifetimes</span></span>

<span data-ttu-id="68b1c-206">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="68b1c-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="68b1c-207">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="68b1c-208">Temporaire</span><span class="sxs-lookup"><span data-stu-id="68b1c-208">Transient</span></span>

<span data-ttu-id="68b1c-209">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="68b1c-210">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="68b1c-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="68b1c-211">Délimité</span><span class="sxs-lookup"><span data-stu-id="68b1c-211">Scoped</span></span>

<span data-ttu-id="68b1c-212">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="68b1c-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="68b1c-213">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="68b1c-214">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="68b1c-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="68b1c-215">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="68b1c-216">Singleton</span><span class="sxs-lookup"><span data-stu-id="68b1c-216">Singleton</span></span>

<span data-ttu-id="68b1c-217">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="68b1c-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="68b1c-218">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="68b1c-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="68b1c-219">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="68b1c-220">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="68b1c-221">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="68b1c-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="68b1c-222">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="68b1c-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="68b1c-223">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="68b1c-223">Service registration methods</span></span>

<span data-ttu-id="68b1c-224">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="68b1c-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="68b1c-225">Méthode</span><span class="sxs-lookup"><span data-stu-id="68b1c-225">Method</span></span> | <span data-ttu-id="68b1c-226">Automatique</span><span class="sxs-lookup"><span data-stu-id="68b1c-226">Automatic</span></span><br><span data-ttu-id="68b1c-227">objet</span><span class="sxs-lookup"><span data-stu-id="68b1c-227">object</span></span><br><span data-ttu-id="68b1c-228">suppression</span><span class="sxs-lookup"><span data-stu-id="68b1c-228">disposal</span></span> | <span data-ttu-id="68b1c-229">Plusieurs</span><span class="sxs-lookup"><span data-stu-id="68b1c-229">Multiple</span></span><br><span data-ttu-id="68b1c-230">implémentations</span><span class="sxs-lookup"><span data-stu-id="68b1c-230">implementations</span></span> | <span data-ttu-id="68b1c-231">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="68b1c-231">Pass args</span></span> |
| ---
<span data-ttu-id="68b1c-232">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-233">'Blazor'</span></span>
- <span data-ttu-id="68b1c-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-234">'Identity'</span></span>
- <span data-ttu-id="68b1c-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-236">'Razor'</span></span>
- <span data-ttu-id="68b1c-237">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-237">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-238">--- | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-239">'Blazor'</span></span>
- <span data-ttu-id="68b1c-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-240">'Identity'</span></span>
- <span data-ttu-id="68b1c-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-242">'Razor'</span></span>
- <span data-ttu-id="68b1c-243">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-244">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-245">'Blazor'</span></span>
- <span data-ttu-id="68b1c-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-246">'Identity'</span></span>
- <span data-ttu-id="68b1c-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-248">'Razor'</span></span>
- <span data-ttu-id="68b1c-249">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-250">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-251">'Blazor'</span></span>
- <span data-ttu-id="68b1c-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-252">'Identity'</span></span>
- <span data-ttu-id="68b1c-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-254">'Razor'</span></span>
- <span data-ttu-id="68b1c-255">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-256">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-257">'Blazor'</span></span>
- <span data-ttu-id="68b1c-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-258">'Identity'</span></span>
- <span data-ttu-id="68b1c-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-260">'Razor'</span></span>
- <span data-ttu-id="68b1c-261">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-262">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-263">'Blazor'</span></span>
- <span data-ttu-id="68b1c-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-264">'Identity'</span></span>
- <span data-ttu-id="68b1c-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-266">'Razor'</span></span>
- <span data-ttu-id="68b1c-267">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-268">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-269">'Blazor'</span></span>
- <span data-ttu-id="68b1c-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-270">'Identity'</span></span>
- <span data-ttu-id="68b1c-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-272">'Razor'</span></span>
- <span data-ttu-id="68b1c-273">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-274">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-275">'Blazor'</span></span>
- <span data-ttu-id="68b1c-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-276">'Identity'</span></span>
- <span data-ttu-id="68b1c-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-278">'Razor'</span></span>
- <span data-ttu-id="68b1c-279">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-280">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-281">'Blazor'</span></span>
- <span data-ttu-id="68b1c-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-282">'Identity'</span></span>
- <span data-ttu-id="68b1c-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-284">'Razor'</span></span>
- <span data-ttu-id="68b1c-285">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-286">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-287">'Blazor'</span></span>
- <span data-ttu-id="68b1c-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-288">'Identity'</span></span>
- <span data-ttu-id="68b1c-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-290">'Razor'</span></span>
- <span data-ttu-id="68b1c-291">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-292">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-293">'Blazor'</span></span>
- <span data-ttu-id="68b1c-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-294">'Identity'</span></span>
- <span data-ttu-id="68b1c-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-296">'Razor'</span></span>
- <span data-ttu-id="68b1c-297">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-298">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-299">'Blazor'</span></span>
- <span data-ttu-id="68b1c-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-300">'Identity'</span></span>
- <span data-ttu-id="68b1c-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-302">'Razor'</span></span>
- <span data-ttu-id="68b1c-303">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-304">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-305">'Blazor'</span></span>
- <span data-ttu-id="68b1c-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-306">'Identity'</span></span>
- <span data-ttu-id="68b1c-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-308">'Razor'</span></span>
- <span data-ttu-id="68b1c-309">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-309">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-310">---------------: | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-311">'Blazor'</span></span>
- <span data-ttu-id="68b1c-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-312">'Identity'</span></span>
- <span data-ttu-id="68b1c-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-314">'Razor'</span></span>
- <span data-ttu-id="68b1c-315">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-316">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-317">'Blazor'</span></span>
- <span data-ttu-id="68b1c-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-318">'Identity'</span></span>
- <span data-ttu-id="68b1c-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-320">'Razor'</span></span>
- <span data-ttu-id="68b1c-321">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-322">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-323">'Blazor'</span></span>
- <span data-ttu-id="68b1c-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-324">'Identity'</span></span>
- <span data-ttu-id="68b1c-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-326">'Razor'</span></span>
- <span data-ttu-id="68b1c-327">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-328">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-329">'Blazor'</span></span>
- <span data-ttu-id="68b1c-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-330">'Identity'</span></span>
- <span data-ttu-id="68b1c-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-332">'Razor'</span></span>
- <span data-ttu-id="68b1c-333">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-334">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-335">'Blazor'</span></span>
- <span data-ttu-id="68b1c-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-336">'Identity'</span></span>
- <span data-ttu-id="68b1c-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-338">'Razor'</span></span>
- <span data-ttu-id="68b1c-339">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-340">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-341">'Blazor'</span></span>
- <span data-ttu-id="68b1c-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-342">'Identity'</span></span>
- <span data-ttu-id="68b1c-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-344">'Razor'</span></span>
- <span data-ttu-id="68b1c-345">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-346">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-347">'Blazor'</span></span>
- <span data-ttu-id="68b1c-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-348">'Identity'</span></span>
- <span data-ttu-id="68b1c-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-350">'Razor'</span></span>
- <span data-ttu-id="68b1c-351">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-352">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-353">'Blazor'</span></span>
- <span data-ttu-id="68b1c-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-354">'Identity'</span></span>
- <span data-ttu-id="68b1c-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-356">'Razor'</span></span>
- <span data-ttu-id="68b1c-357">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-358">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-359">'Blazor'</span></span>
- <span data-ttu-id="68b1c-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-360">'Identity'</span></span>
- <span data-ttu-id="68b1c-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-362">'Razor'</span></span>
- <span data-ttu-id="68b1c-363">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-364">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-365">'Blazor'</span></span>
- <span data-ttu-id="68b1c-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-366">'Identity'</span></span>
- <span data-ttu-id="68b1c-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-368">'Razor'</span></span>
- <span data-ttu-id="68b1c-369">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-369">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-370">-------------: | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-371">'Blazor'</span></span>
- <span data-ttu-id="68b1c-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-372">'Identity'</span></span>
- <span data-ttu-id="68b1c-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-374">'Razor'</span></span>
- <span data-ttu-id="68b1c-375">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-375">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="68b1c-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="68b1c-377">Exemple :</span><span class="sxs-lookup"><span data-stu-id="68b1c-377">Example:</span></span><br><span data-ttu-id="68b1c-378">`services.AddSingleton<IMyDep, MyDep>();`| Oui | Oui | Non | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="68b1c-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="68b1c-379">Exemples :</span><span class="sxs-lookup"><span data-stu-id="68b1c-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="68b1c-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Oui | Oui | Oui | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="68b1c-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="68b1c-381">Exemple :</span><span class="sxs-lookup"><span data-stu-id="68b1c-381">Example:</span></span><br><span data-ttu-id="68b1c-382">`services.AddSingleton<MyDep>();`| Oui | Non | Non | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="68b1c-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="68b1c-383">Exemples :</span><span class="sxs-lookup"><span data-stu-id="68b1c-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="68b1c-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Non | Oui | Oui | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="68b1c-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="68b1c-385">Exemples :</span><span class="sxs-lookup"><span data-stu-id="68b1c-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="68b1c-386">`services.AddSingleton(new MyDep("A string!"));`| Non | Non | Oui |</span><span class="sxs-lookup"><span data-stu-id="68b1c-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="68b1c-387">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="68b1c-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="68b1c-388">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="68b1c-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="68b1c-389">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="68b1c-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="68b1c-390">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="68b1c-391">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="68b1c-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="68b1c-392">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="68b1c-393">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="68b1c-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="68b1c-394">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="68b1c-395">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="68b1c-396">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="68b1c-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="68b1c-397">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="68b1c-398">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="68b1c-399">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="68b1c-400">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="68b1c-400">Constructor injection behavior</span></span>

<span data-ttu-id="68b1c-401">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="68b1c-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="68b1c-403">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="68b1c-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="68b1c-404">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="68b1c-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="68b1c-405">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="68b1c-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="68b1c-406">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="68b1c-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="68b1c-407">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="68b1c-408">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="68b1c-408">Entity Framework contexts</span></span>

<span data-ttu-id="68b1c-409">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="68b1c-410">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="68b1c-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="68b1c-411">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="68b1c-412">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="68b1c-412">Lifetime and registration options</span></span>

<span data-ttu-id="68b1c-413">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="68b1c-414">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="68b1c-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="68b1c-415">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="68b1c-416">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="68b1c-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="68b1c-417">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="68b1c-418">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="68b1c-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="68b1c-419">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="68b1c-420">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="68b1c-421">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="68b1c-422">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="68b1c-423">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="68b1c-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="68b1c-424">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="68b1c-425">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="68b1c-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="68b1c-426">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="68b1c-427">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="68b1c-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="68b1c-428">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="68b1c-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="68b1c-429">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="68b1c-430">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="68b1c-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="68b1c-431">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="68b1c-432">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="68b1c-432">**First request:**</span></span>

<span data-ttu-id="68b1c-433">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="68b1c-433">Controller operations:</span></span>

<span data-ttu-id="68b1c-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="68b1c-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="68b1c-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="68b1c-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="68b1c-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-437">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-438">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-438">`OperationService` operations:</span></span>

<span data-ttu-id="68b1c-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="68b1c-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="68b1c-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="68b1c-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="68b1c-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-442">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-443">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="68b1c-443">**Second request:**</span></span>

<span data-ttu-id="68b1c-444">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="68b1c-444">Controller operations:</span></span>

<span data-ttu-id="68b1c-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="68b1c-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="68b1c-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="68b1c-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="68b1c-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-448">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-449">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-449">`OperationService` operations:</span></span>

<span data-ttu-id="68b1c-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="68b1c-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="68b1c-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="68b1c-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="68b1c-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-453">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-454">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="68b1c-455">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="68b1c-455">*Transient* objects are always different.</span></span> <span data-ttu-id="68b1c-456">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="68b1c-457">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="68b1c-458">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="68b1c-459">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="68b1c-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="68b1c-460">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="68b1c-460">Call services from main</span></span>

<span data-ttu-id="68b1c-461">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="68b1c-462">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="68b1c-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="68b1c-463">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="68b1c-464">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="68b1c-464">Scope validation</span></span>

<span data-ttu-id="68b1c-465">Lorsque l’application s’exécute dans l’environnement de développement et appelle [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) pour générer l’hôte, le fournisseur de services par défaut effectue des vérifications pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="68b1c-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="68b1c-466">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="68b1c-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="68b1c-467">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="68b1c-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="68b1c-468">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="68b1c-469">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="68b1c-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="68b1c-470">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="68b1c-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="68b1c-471">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="68b1c-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="68b1c-472">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="68b1c-473">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="68b1c-474">Services de requête</span><span class="sxs-lookup"><span data-stu-id="68b1c-474">Request Services</span></span>

<span data-ttu-id="68b1c-475">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="68b1c-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="68b1c-476">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="68b1c-477">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="68b1c-478">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="68b1c-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="68b1c-479">Au lieu de cela, demandez les types que les classes requièrent via les constructeurs de classe et autorisez l’infrastructure à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="68b1c-480">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="68b1c-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="68b1c-481">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="68b1c-482">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="68b1c-482">Design services for dependency injection</span></span>

<span data-ttu-id="68b1c-483">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="68b1c-483">Best practices are to:</span></span>

* <span data-ttu-id="68b1c-484">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="68b1c-485">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="68b1c-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="68b1c-486">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="68b1c-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="68b1c-487">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="68b1c-488">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="68b1c-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="68b1c-489">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="68b1c-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="68b1c-490">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="68b1c-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="68b1c-491">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="68b1c-492">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="68b1c-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="68b1c-493">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="68b1c-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="68b1c-494">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="68b1c-494">Disposal of services</span></span>

<span data-ttu-id="68b1c-495">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="68b1c-496">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="68b1c-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="68b1c-497">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="68b1c-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="68b1c-498">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="68b1c-498">In the following example:</span></span>

* <span data-ttu-id="68b1c-499">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="68b1c-500">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="68b1c-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="68b1c-501">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="68b1c-502">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="68b1c-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="68b1c-503">Pour une description des options de suppression de service, consultez [quatre façons de supprimer des IDisposable dans ASP.net Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="68b1c-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="68b1c-504">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="68b1c-504">Default service container replacement</span></span>

<span data-ttu-id="68b1c-505">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="68b1c-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="68b1c-506">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="68b1c-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="68b1c-507">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="68b1c-507">Property injection</span></span>
* <span data-ttu-id="68b1c-508">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="68b1c-508">Injection based on name</span></span>
* <span data-ttu-id="68b1c-509">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="68b1c-509">Child containers</span></span>
* <span data-ttu-id="68b1c-510">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="68b1c-510">Custom lifetime management</span></span>
* <span data-ttu-id="68b1c-511">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="68b1c-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="68b1c-512">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="68b1c-512">Convention-based registration</span></span>

<span data-ttu-id="68b1c-513">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="68b1c-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="68b1c-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="68b1c-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="68b1c-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="68b1c-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="68b1c-516">Grace</span><span class="sxs-lookup"><span data-stu-id="68b1c-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="68b1c-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="68b1c-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="68b1c-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="68b1c-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="68b1c-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="68b1c-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="68b1c-520">Unity</span><span class="sxs-lookup"><span data-stu-id="68b1c-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="68b1c-521">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="68b1c-521">Thread safety</span></span>

<span data-ttu-id="68b1c-522">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="68b1c-523">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="68b1c-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="68b1c-524">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="68b1c-525">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="68b1c-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="68b1c-526">Recommandations</span><span class="sxs-lookup"><span data-stu-id="68b1c-526">Recommendations</span></span>

* <span data-ttu-id="68b1c-527">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="68b1c-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="68b1c-528">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="68b1c-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="68b1c-529">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="68b1c-530">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="68b1c-531">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="68b1c-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="68b1c-532">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="68b1c-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="68b1c-533">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="68b1c-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="68b1c-534">Évitez l’accès statique aux services (par exemple avec [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) à utiliser ailleurs).</span><span class="sxs-lookup"><span data-stu-id="68b1c-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="68b1c-535">Évitez d’utiliser le *modèle de localisation de service*.</span><span class="sxs-lookup"><span data-stu-id="68b1c-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="68b1c-536">Par exemple, n’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service si vous pouvez utiliser l’injection de dépendance à la place :</span><span class="sxs-lookup"><span data-stu-id="68b1c-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="68b1c-537">**Correcte**</span><span class="sxs-lookup"><span data-stu-id="68b1c-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="68b1c-538">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="68b1c-538">**Correct**:</span></span>

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

* <span data-ttu-id="68b1c-539">Une autre variante du localisateur de service à éviter est l’injection d’une fabrique qui résout les dépendances au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="68b1c-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="68b1c-540">Ces deux pratiques combinent des stratégies [Inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="68b1c-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="68b1c-541">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="68b1c-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="68b1c-542">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="68b1c-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="68b1c-543">Les exceptions sont rares et représentent principalement des cas spéciaux dans le framework lui-même.</span><span class="sxs-lookup"><span data-stu-id="68b1c-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="68b1c-544">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="68b1c-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="68b1c-545">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="68b1c-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="68b1c-546">Modèles recommandés pour l’architecture mutualisée dans DI</span><span class="sxs-lookup"><span data-stu-id="68b1c-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="68b1c-547">Le [cœur du verger](https://github.com/OrchardCMS/OrchardCore) fournit une architecture mutualisée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="68b1c-548">Pour plus d’informations, consultez la documentation sur le [noyau du verger](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="68b1c-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="68b1c-549">Consultez les exemples d’applications à l’adresse https://github.com/OrchardCMS/OrchardCore.Samples pour obtenir des exemples de création d’applications modulaires et mutualisées à l’aide de l’infrastructure principale du verger uniquement sans les fonctionnalités spécifiques au CMS.</span><span class="sxs-lookup"><span data-stu-id="68b1c-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68b1c-550">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="68b1c-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="68b1c-551">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="68b1c-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="68b1c-552">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="68b1c-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="68b1c-553">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="68b1c-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="68b1c-554">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68b1c-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68b1c-555">ASP.NET Core prend en charge le modèle de conception de logiciel avec injection de dépendances (DI), une technique permettant d’obtenir une [inversion de contrôle (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="68b1c-556">Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="68b1c-557">[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68b1c-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="68b1c-558">Vue d’ensemble de l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="68b1c-558">Overview of dependency injection</span></span>

<span data-ttu-id="68b1c-559">Une *dépendance* est un objet qui nécessite un autre objet.</span><span class="sxs-lookup"><span data-stu-id="68b1c-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="68b1c-560">Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :</span><span class="sxs-lookup"><span data-stu-id="68b1c-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="68b1c-561">Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="68b1c-562">La classe `MyDependency` est une dépendance de la classe `IndexModel` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="68b1c-563">La classe est créee et dépend directement de l’instance `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="68b1c-564">Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="68b1c-565">Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="68b1c-566">Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="68b1c-567">Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="68b1c-568">Cette implémentation complique le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="68b1c-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="68b1c-569">L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="68b1c-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="68b1c-570">L’injection de dépendances résout ces problèmes via :</span><span class="sxs-lookup"><span data-stu-id="68b1c-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="68b1c-571">L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="68b1c-572">L’inscription de la dépendance dans un conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="68b1c-573">ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="68b1c-574">Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="68b1c-575">*Injection* du service dans le constructeur de la classe où il est utilisé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="68b1c-576">Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="68b1c-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="68b1c-577">Dans l’[exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :</span><span class="sxs-lookup"><span data-stu-id="68b1c-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="68b1c-578">Cette interface est implémentée par un type concret, `MyDependency` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="68b1c-579">`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur.</span><span class="sxs-lookup"><span data-stu-id="68b1c-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="68b1c-580">Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="68b1c-581">Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="68b1c-582">Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu.</span><span class="sxs-lookup"><span data-stu-id="68b1c-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="68b1c-583">L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.</span><span class="sxs-lookup"><span data-stu-id="68b1c-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="68b1c-584">`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="68b1c-585">`IMyDependency` est inscrit dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="68b1c-586">`ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="68b1c-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="68b1c-587">Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :</span><span class="sxs-lookup"><span data-stu-id="68b1c-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="68b1c-588">Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="68b1c-589">L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique.</span><span class="sxs-lookup"><span data-stu-id="68b1c-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="68b1c-590">Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="68b1c-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="68b1c-591">Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="68b1c-592">Par exemple, `services.AddMvc()` ajoute les pages de services Razor et MVC requièrent.</span><span class="sxs-lookup"><span data-stu-id="68b1c-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="68b1c-593">Il est recommandé que les applications suivent cette convention.</span><span class="sxs-lookup"><span data-stu-id="68b1c-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="68b1c-594">Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="68b1c-595">Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :</span><span class="sxs-lookup"><span data-stu-id="68b1c-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="68b1c-596">Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="68b1c-597">Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="68b1c-598">Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :</span><span class="sxs-lookup"><span data-stu-id="68b1c-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="68b1c-599">Services injectés au démarrage</span><span class="sxs-lookup"><span data-stu-id="68b1c-599">Services injected into Startup</span></span>

<span data-ttu-id="68b1c-600">Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :</span><span class="sxs-lookup"><span data-stu-id="68b1c-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="68b1c-601">Les services peuvent être injectés dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="68b1c-602">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="68b1c-603">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="68b1c-603">Framework-provided services</span></span>

<span data-ttu-id="68b1c-604">La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="68b1c-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="68b1c-605">Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="68b1c-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="68b1c-606">Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="68b1c-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="68b1c-607">Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="68b1c-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="68b1c-608">Type de service</span><span class="sxs-lookup"><span data-stu-id="68b1c-608">Service Type</span></span> | <span data-ttu-id="68b1c-609">Durée de vie</span><span class="sxs-lookup"><span data-stu-id="68b1c-609">Lifetime</span></span> |
| ---
<span data-ttu-id="68b1c-610">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-611">'Blazor'</span></span>
- <span data-ttu-id="68b1c-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-612">'Identity'</span></span>
- <span data-ttu-id="68b1c-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-614">'Razor'</span></span>
- <span data-ttu-id="68b1c-615">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-616">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-617">'Blazor'</span></span>
- <span data-ttu-id="68b1c-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-618">'Identity'</span></span>
- <span data-ttu-id="68b1c-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-620">'Razor'</span></span>
- <span data-ttu-id="68b1c-621">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-622">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-623">'Blazor'</span></span>
- <span data-ttu-id="68b1c-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-624">'Identity'</span></span>
- <span data-ttu-id="68b1c-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-626">'Razor'</span></span>
- <span data-ttu-id="68b1c-627">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-628">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-629">'Blazor'</span></span>
- <span data-ttu-id="68b1c-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-630">'Identity'</span></span>
- <span data-ttu-id="68b1c-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-632">'Razor'</span></span>
- <span data-ttu-id="68b1c-633">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-633">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-634">------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-635">'Blazor'</span></span>
- <span data-ttu-id="68b1c-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-636">'Identity'</span></span>
- <span data-ttu-id="68b1c-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-638">'Razor'</span></span>
- <span data-ttu-id="68b1c-639">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-640">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-641">'Blazor'</span></span>
- <span data-ttu-id="68b1c-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-642">'Identity'</span></span>
- <span data-ttu-id="68b1c-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-644">'Razor'</span></span>
- <span data-ttu-id="68b1c-645">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-645">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaire | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaire | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaire | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaire | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="68b1c-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="68b1c-647">Inscrire des services supplémentaires avec les méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="68b1c-647">Register additional services with extension methods</span></span>

<span data-ttu-id="68b1c-648">Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="68b1c-649">Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :</span><span class="sxs-lookup"><span data-stu-id="68b1c-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="68b1c-650">Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.</span><span class="sxs-lookup"><span data-stu-id="68b1c-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="68b1c-651">Durées de service</span><span class="sxs-lookup"><span data-stu-id="68b1c-651">Service lifetimes</span></span>

<span data-ttu-id="68b1c-652">Choisissez une durée de vie appropriée pour chaque service inscrit.</span><span class="sxs-lookup"><span data-stu-id="68b1c-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="68b1c-653">Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="68b1c-654">Temporaire</span><span class="sxs-lookup"><span data-stu-id="68b1c-654">Transient</span></span>

<span data-ttu-id="68b1c-655">Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="68b1c-656">Cette durée de vie convient parfaitement aux services légers et sans état.</span><span class="sxs-lookup"><span data-stu-id="68b1c-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="68b1c-657">Délimité</span><span class="sxs-lookup"><span data-stu-id="68b1c-657">Scoped</span></span>

<span data-ttu-id="68b1c-658">Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).</span><span class="sxs-lookup"><span data-stu-id="68b1c-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="68b1c-659">Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="68b1c-660">N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton.</span><span class="sxs-lookup"><span data-stu-id="68b1c-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="68b1c-661">Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="68b1c-662">Singleton</span><span class="sxs-lookup"><span data-stu-id="68b1c-662">Singleton</span></span>

<span data-ttu-id="68b1c-663">Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service).</span><span class="sxs-lookup"><span data-stu-id="68b1c-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="68b1c-664">Chaque requête ultérieure utilise la même instance.</span><span class="sxs-lookup"><span data-stu-id="68b1c-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="68b1c-665">Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="68b1c-666">N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="68b1c-667">Il est dangereux de résoudre un service délimité depuis un singleton.</span><span class="sxs-lookup"><span data-stu-id="68b1c-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="68b1c-668">L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="68b1c-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="68b1c-669">Méthodes d’inscription du service</span><span class="sxs-lookup"><span data-stu-id="68b1c-669">Service registration methods</span></span>

<span data-ttu-id="68b1c-670">Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.</span><span class="sxs-lookup"><span data-stu-id="68b1c-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="68b1c-671">Méthode</span><span class="sxs-lookup"><span data-stu-id="68b1c-671">Method</span></span> | <span data-ttu-id="68b1c-672">Automatique</span><span class="sxs-lookup"><span data-stu-id="68b1c-672">Automatic</span></span><br><span data-ttu-id="68b1c-673">objet</span><span class="sxs-lookup"><span data-stu-id="68b1c-673">object</span></span><br><span data-ttu-id="68b1c-674">suppression</span><span class="sxs-lookup"><span data-stu-id="68b1c-674">disposal</span></span> | <span data-ttu-id="68b1c-675">Plusieurs</span><span class="sxs-lookup"><span data-stu-id="68b1c-675">Multiple</span></span><br><span data-ttu-id="68b1c-676">implémentations</span><span class="sxs-lookup"><span data-stu-id="68b1c-676">implementations</span></span> | <span data-ttu-id="68b1c-677">Passage d’args</span><span class="sxs-lookup"><span data-stu-id="68b1c-677">Pass args</span></span> |
| ---
<span data-ttu-id="68b1c-678">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-679">'Blazor'</span></span>
- <span data-ttu-id="68b1c-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-680">'Identity'</span></span>
- <span data-ttu-id="68b1c-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-682">'Razor'</span></span>
- <span data-ttu-id="68b1c-683">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-683">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-684">--- | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-685">'Blazor'</span></span>
- <span data-ttu-id="68b1c-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-686">'Identity'</span></span>
- <span data-ttu-id="68b1c-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-688">'Razor'</span></span>
- <span data-ttu-id="68b1c-689">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-690">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-691">'Blazor'</span></span>
- <span data-ttu-id="68b1c-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-692">'Identity'</span></span>
- <span data-ttu-id="68b1c-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-694">'Razor'</span></span>
- <span data-ttu-id="68b1c-695">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-696">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-697">'Blazor'</span></span>
- <span data-ttu-id="68b1c-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-698">'Identity'</span></span>
- <span data-ttu-id="68b1c-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-700">'Razor'</span></span>
- <span data-ttu-id="68b1c-701">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-702">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-703">'Blazor'</span></span>
- <span data-ttu-id="68b1c-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-704">'Identity'</span></span>
- <span data-ttu-id="68b1c-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-706">'Razor'</span></span>
- <span data-ttu-id="68b1c-707">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-708">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-709">'Blazor'</span></span>
- <span data-ttu-id="68b1c-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-710">'Identity'</span></span>
- <span data-ttu-id="68b1c-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-712">'Razor'</span></span>
- <span data-ttu-id="68b1c-713">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-714">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-715">'Blazor'</span></span>
- <span data-ttu-id="68b1c-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-716">'Identity'</span></span>
- <span data-ttu-id="68b1c-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-718">'Razor'</span></span>
- <span data-ttu-id="68b1c-719">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-720">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-721">'Blazor'</span></span>
- <span data-ttu-id="68b1c-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-722">'Identity'</span></span>
- <span data-ttu-id="68b1c-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-724">'Razor'</span></span>
- <span data-ttu-id="68b1c-725">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-726">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-727">'Blazor'</span></span>
- <span data-ttu-id="68b1c-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-728">'Identity'</span></span>
- <span data-ttu-id="68b1c-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-730">'Razor'</span></span>
- <span data-ttu-id="68b1c-731">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-732">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-733">'Blazor'</span></span>
- <span data-ttu-id="68b1c-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-734">'Identity'</span></span>
- <span data-ttu-id="68b1c-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-736">'Razor'</span></span>
- <span data-ttu-id="68b1c-737">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-738">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-739">'Blazor'</span></span>
- <span data-ttu-id="68b1c-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-740">'Identity'</span></span>
- <span data-ttu-id="68b1c-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-742">'Razor'</span></span>
- <span data-ttu-id="68b1c-743">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-744">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-745">'Blazor'</span></span>
- <span data-ttu-id="68b1c-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-746">'Identity'</span></span>
- <span data-ttu-id="68b1c-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-748">'Razor'</span></span>
- <span data-ttu-id="68b1c-749">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-750">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-751">'Blazor'</span></span>
- <span data-ttu-id="68b1c-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-752">'Identity'</span></span>
- <span data-ttu-id="68b1c-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-754">'Razor'</span></span>
- <span data-ttu-id="68b1c-755">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-755">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-756">---------------: | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-757">'Blazor'</span></span>
- <span data-ttu-id="68b1c-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-758">'Identity'</span></span>
- <span data-ttu-id="68b1c-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-760">'Razor'</span></span>
- <span data-ttu-id="68b1c-761">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-762">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-763">'Blazor'</span></span>
- <span data-ttu-id="68b1c-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-764">'Identity'</span></span>
- <span data-ttu-id="68b1c-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-766">'Razor'</span></span>
- <span data-ttu-id="68b1c-767">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-768">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-769">'Blazor'</span></span>
- <span data-ttu-id="68b1c-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-770">'Identity'</span></span>
- <span data-ttu-id="68b1c-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-772">'Razor'</span></span>
- <span data-ttu-id="68b1c-773">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-774">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-775">'Blazor'</span></span>
- <span data-ttu-id="68b1c-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-776">'Identity'</span></span>
- <span data-ttu-id="68b1c-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-778">'Razor'</span></span>
- <span data-ttu-id="68b1c-779">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-780">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-781">'Blazor'</span></span>
- <span data-ttu-id="68b1c-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-782">'Identity'</span></span>
- <span data-ttu-id="68b1c-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-784">'Razor'</span></span>
- <span data-ttu-id="68b1c-785">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-786">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-787">'Blazor'</span></span>
- <span data-ttu-id="68b1c-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-788">'Identity'</span></span>
- <span data-ttu-id="68b1c-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-790">'Razor'</span></span>
- <span data-ttu-id="68b1c-791">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-792">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-793">'Blazor'</span></span>
- <span data-ttu-id="68b1c-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-794">'Identity'</span></span>
- <span data-ttu-id="68b1c-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-796">'Razor'</span></span>
- <span data-ttu-id="68b1c-797">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-798">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-799">'Blazor'</span></span>
- <span data-ttu-id="68b1c-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-800">'Identity'</span></span>
- <span data-ttu-id="68b1c-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-802">'Razor'</span></span>
- <span data-ttu-id="68b1c-803">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-804">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-805">'Blazor'</span></span>
- <span data-ttu-id="68b1c-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-806">'Identity'</span></span>
- <span data-ttu-id="68b1c-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-808">'Razor'</span></span>
- <span data-ttu-id="68b1c-809">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68b1c-810">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-811">'Blazor'</span></span>
- <span data-ttu-id="68b1c-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-812">'Identity'</span></span>
- <span data-ttu-id="68b1c-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-814">'Razor'</span></span>
- <span data-ttu-id="68b1c-815">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-815">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-816">-------------: | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="68b1c-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68b1c-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-817">'Blazor'</span></span>
- <span data-ttu-id="68b1c-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68b1c-818">'Identity'</span></span>
- <span data-ttu-id="68b1c-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68b1c-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="68b1c-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68b1c-820">'Razor'</span></span>
- <span data-ttu-id="68b1c-821">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="68b1c-821">'SignalR' uid:</span></span> 

<span data-ttu-id="68b1c-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="68b1c-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="68b1c-823">Exemple :</span><span class="sxs-lookup"><span data-stu-id="68b1c-823">Example:</span></span><br><span data-ttu-id="68b1c-824">`services.AddSingleton<IMyDep, MyDep>();`| Oui | Oui | Non | |`Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="68b1c-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="68b1c-825">Exemples :</span><span class="sxs-lookup"><span data-stu-id="68b1c-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="68b1c-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));`| Oui | Oui | Oui | |`Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="68b1c-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="68b1c-827">Exemple :</span><span class="sxs-lookup"><span data-stu-id="68b1c-827">Example:</span></span><br><span data-ttu-id="68b1c-828">`services.AddSingleton<MyDep>();`| Oui | Non | Non | |`AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="68b1c-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="68b1c-829">Exemples :</span><span class="sxs-lookup"><span data-stu-id="68b1c-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="68b1c-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));`| Non | Oui | Oui | |`AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="68b1c-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="68b1c-831">Exemples :</span><span class="sxs-lookup"><span data-stu-id="68b1c-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="68b1c-832">`services.AddSingleton(new MyDep("A string!"));`| Non | Non | Oui |</span><span class="sxs-lookup"><span data-stu-id="68b1c-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="68b1c-833">Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="68b1c-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="68b1c-834">Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="68b1c-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="68b1c-835">Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.</span><span class="sxs-lookup"><span data-stu-id="68b1c-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="68b1c-836">Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="68b1c-837">La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :</span><span class="sxs-lookup"><span data-stu-id="68b1c-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="68b1c-838">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="68b1c-839">Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*.</span><span class="sxs-lookup"><span data-stu-id="68b1c-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="68b1c-840">Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="68b1c-841">Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="68b1c-842">En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="68b1c-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="68b1c-843">Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="68b1c-844">La deuxième ligne inscrit `MyDep` pour `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="68b1c-845">La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="68b1c-846">Comportement d’injection de constructeurs</span><span class="sxs-lookup"><span data-stu-id="68b1c-846">Constructor injection behavior</span></span>

<span data-ttu-id="68b1c-847">Les services peuvent être résolus par deux mécanismes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="68b1c-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="68b1c-849">`ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.</span><span class="sxs-lookup"><span data-stu-id="68b1c-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="68b1c-850">Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="68b1c-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="68b1c-851">Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .</span><span class="sxs-lookup"><span data-stu-id="68b1c-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="68b1c-852">Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable.</span><span class="sxs-lookup"><span data-stu-id="68b1c-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="68b1c-853">Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="68b1c-854">Contextes Entity Framework</span><span class="sxs-lookup"><span data-stu-id="68b1c-854">Entity Framework contexts</span></span>

<span data-ttu-id="68b1c-855">Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="68b1c-856">La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="68b1c-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="68b1c-857">Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="68b1c-858">Options de durée de vie et d’inscription</span><span class="sxs-lookup"><span data-stu-id="68b1c-858">Lifetime and registration options</span></span>

<span data-ttu-id="68b1c-859">Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="68b1c-860">Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :</span><span class="sxs-lookup"><span data-stu-id="68b1c-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="68b1c-861">Les interfaces sont implémentées dans la classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="68b1c-862">Le constructeur `Operation` génère un GUID s’il n’est pas fourni :</span><span class="sxs-lookup"><span data-stu-id="68b1c-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="68b1c-863">Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="68b1c-864">Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.</span><span class="sxs-lookup"><span data-stu-id="68b1c-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="68b1c-865">Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="68b1c-866">`OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="68b1c-867">La nouvelle instance génère un autre `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="68b1c-868">Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="68b1c-869">Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.</span><span class="sxs-lookup"><span data-stu-id="68b1c-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="68b1c-870">Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="68b1c-871">Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :</span><span class="sxs-lookup"><span data-stu-id="68b1c-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="68b1c-872">Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="68b1c-873">L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).</span><span class="sxs-lookup"><span data-stu-id="68b1c-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="68b1c-874">L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles.</span><span class="sxs-lookup"><span data-stu-id="68b1c-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="68b1c-875">L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="68b1c-876">La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :</span><span class="sxs-lookup"><span data-stu-id="68b1c-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="68b1c-877">Les deux sorties suivantes montrent les résultats de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="68b1c-878">**Première requête :**</span><span class="sxs-lookup"><span data-stu-id="68b1c-878">**First request:**</span></span>

<span data-ttu-id="68b1c-879">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="68b1c-879">Controller operations:</span></span>

<span data-ttu-id="68b1c-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="68b1c-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="68b1c-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="68b1c-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="68b1c-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-883">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-884">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-884">`OperationService` operations:</span></span>

<span data-ttu-id="68b1c-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="68b1c-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="68b1c-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="68b1c-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="68b1c-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-888">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-889">**Deuxième requête :**</span><span class="sxs-lookup"><span data-stu-id="68b1c-889">**Second request:**</span></span>

<span data-ttu-id="68b1c-890">Opérations du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="68b1c-890">Controller operations:</span></span>

<span data-ttu-id="68b1c-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="68b1c-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="68b1c-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="68b1c-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="68b1c-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-894">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-895">Opérations `OperationService` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-895">`OperationService` operations:</span></span>

<span data-ttu-id="68b1c-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="68b1c-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="68b1c-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="68b1c-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="68b1c-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="68b1c-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="68b1c-899">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="68b1c-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="68b1c-900">Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :</span><span class="sxs-lookup"><span data-stu-id="68b1c-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="68b1c-901">Les objets *Transient* sont toujours différents.</span><span class="sxs-lookup"><span data-stu-id="68b1c-901">*Transient* objects are always different.</span></span> <span data-ttu-id="68b1c-902">Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="68b1c-903">Une nouvelle instance est fournie à chaque requête de service et requête de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="68b1c-904">Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.</span><span class="sxs-lookup"><span data-stu-id="68b1c-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="68b1c-905">Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="68b1c-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="68b1c-906">Appeler des services à partir de Main</span><span class="sxs-lookup"><span data-stu-id="68b1c-906">Call services from main</span></span>

<span data-ttu-id="68b1c-907">Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="68b1c-908">Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation.</span><span class="sxs-lookup"><span data-stu-id="68b1c-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="68b1c-909">L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="68b1c-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="68b1c-910">Validation de l’étendue</span><span class="sxs-lookup"><span data-stu-id="68b1c-910">Scope validation</span></span>

<span data-ttu-id="68b1c-911">Quand l’application s’exécute dans l’environnement de développement, le fournisseur de services par défaut effectue des contrôles pour vérifier que :</span><span class="sxs-lookup"><span data-stu-id="68b1c-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="68b1c-912">Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.</span><span class="sxs-lookup"><span data-stu-id="68b1c-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="68b1c-913">Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.</span><span class="sxs-lookup"><span data-stu-id="68b1c-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="68b1c-914">Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="68b1c-915">La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="68b1c-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="68b1c-916">Les services Scoped sont supprimés par le conteneur qui les a créés.</span><span class="sxs-lookup"><span data-stu-id="68b1c-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="68b1c-917">Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté.</span><span class="sxs-lookup"><span data-stu-id="68b1c-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="68b1c-918">La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.</span><span class="sxs-lookup"><span data-stu-id="68b1c-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="68b1c-919">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="68b1c-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="68b1c-920">Services de requête</span><span class="sxs-lookup"><span data-stu-id="68b1c-920">Request Services</span></span>

<span data-ttu-id="68b1c-921">Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="68b1c-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="68b1c-922">Les services de requête représentent les services configurés et demandés dans le cadre de l’application.</span><span class="sxs-lookup"><span data-stu-id="68b1c-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="68b1c-923">Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="68b1c-924">En règle générale, l’application ne doit pas utiliser directement ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="68b1c-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="68b1c-925">Demandez plutôt les types nécessaires à la classe via des constructeurs de classe et autorisez le framework à injecter les dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="68b1c-926">Cela génère des classes qui sont plus faciles à tester.</span><span class="sxs-lookup"><span data-stu-id="68b1c-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="68b1c-927">Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="68b1c-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="68b1c-928">Conception de services pour l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="68b1c-928">Design services for dependency injection</span></span>

<span data-ttu-id="68b1c-929">Les bonnes pratiques permettent de :</span><span class="sxs-lookup"><span data-stu-id="68b1c-929">Best practices are to:</span></span>

* <span data-ttu-id="68b1c-930">Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.</span><span class="sxs-lookup"><span data-stu-id="68b1c-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="68b1c-931">Évitez les classes et les membres avec état, static.</span><span class="sxs-lookup"><span data-stu-id="68b1c-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="68b1c-932">Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.</span><span class="sxs-lookup"><span data-stu-id="68b1c-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="68b1c-933">Éviter une instanciation directe de classes dépendantes au sein de services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="68b1c-934">L’instanciation directe associe le code à une implémentation particulière.</span><span class="sxs-lookup"><span data-stu-id="68b1c-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="68b1c-935">Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.</span><span class="sxs-lookup"><span data-stu-id="68b1c-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="68b1c-936">Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="68b1c-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="68b1c-937">Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="68b1c-938">Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="68b1c-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="68b1c-939">Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="68b1c-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="68b1c-940">Suppression des services</span><span class="sxs-lookup"><span data-stu-id="68b1c-940">Disposal of services</span></span>

<span data-ttu-id="68b1c-941">Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée.</span><span class="sxs-lookup"><span data-stu-id="68b1c-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="68b1c-942">Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="68b1c-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="68b1c-943">Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="68b1c-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="68b1c-944">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="68b1c-944">In the following example:</span></span>

* <span data-ttu-id="68b1c-945">Les instances de service ne sont pas créées par le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="68b1c-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="68b1c-946">Les durées de vie de service prévues ne sont pas connues de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="68b1c-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="68b1c-947">L’infrastructure ne supprime pas automatiquement les services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="68b1c-948">Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="68b1c-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="68b1c-949">Remplacement de conteneur de services par défaut</span><span class="sxs-lookup"><span data-stu-id="68b1c-949">Default service container replacement</span></span>

<span data-ttu-id="68b1c-950">Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public.</span><span class="sxs-lookup"><span data-stu-id="68b1c-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="68b1c-951">Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :</span><span class="sxs-lookup"><span data-stu-id="68b1c-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="68b1c-952">Injection de propriétés</span><span class="sxs-lookup"><span data-stu-id="68b1c-952">Property injection</span></span>
* <span data-ttu-id="68b1c-953">Injection en fonction du nom</span><span class="sxs-lookup"><span data-stu-id="68b1c-953">Injection based on name</span></span>
* <span data-ttu-id="68b1c-954">Conteneurs enfants</span><span class="sxs-lookup"><span data-stu-id="68b1c-954">Child containers</span></span>
* <span data-ttu-id="68b1c-955">Gestion personnalisée de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="68b1c-955">Custom lifetime management</span></span>
* <span data-ttu-id="68b1c-956">`Func<T>` prend en charge l’initialisation tardive</span><span class="sxs-lookup"><span data-stu-id="68b1c-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="68b1c-957">Inscription basée sur une convention</span><span class="sxs-lookup"><span data-stu-id="68b1c-957">Convention-based registration</span></span>

<span data-ttu-id="68b1c-958">Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="68b1c-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="68b1c-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="68b1c-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="68b1c-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="68b1c-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="68b1c-961">Grace</span><span class="sxs-lookup"><span data-stu-id="68b1c-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="68b1c-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="68b1c-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="68b1c-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="68b1c-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="68b1c-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="68b1c-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="68b1c-965">Unity</span><span class="sxs-lookup"><span data-stu-id="68b1c-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="68b1c-966">Sécurité des threads</span><span class="sxs-lookup"><span data-stu-id="68b1c-966">Thread safety</span></span>

<span data-ttu-id="68b1c-967">Créez des services singleton thread-safe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="68b1c-968">Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.</span><span class="sxs-lookup"><span data-stu-id="68b1c-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="68b1c-969">La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe.</span><span class="sxs-lookup"><span data-stu-id="68b1c-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="68b1c-970">Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.</span><span class="sxs-lookup"><span data-stu-id="68b1c-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="68b1c-971">Recommandations</span><span class="sxs-lookup"><span data-stu-id="68b1c-971">Recommendations</span></span>

* <span data-ttu-id="68b1c-972">La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="68b1c-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="68b1c-973">C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.</span><span class="sxs-lookup"><span data-stu-id="68b1c-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="68b1c-974">Évitez de stocker des données et des configurations directement dans le conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="68b1c-975">Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="68b1c-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="68b1c-976">La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="68b1c-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="68b1c-977">De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet.</span><span class="sxs-lookup"><span data-stu-id="68b1c-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="68b1c-978">Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="68b1c-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="68b1c-979">Évitez l’accès statique aux services (par exemple avec [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) à utiliser ailleurs).</span><span class="sxs-lookup"><span data-stu-id="68b1c-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="68b1c-980">Évitez d’utiliser le *modèle de localisation de service*, qui combine les stratégies [d’inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="68b1c-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="68b1c-981">N’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service lorsque vous pouvez utiliser l’injection de code à la place :</span><span class="sxs-lookup"><span data-stu-id="68b1c-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="68b1c-982">**Correcte**</span><span class="sxs-lookup"><span data-stu-id="68b1c-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="68b1c-983">**Correct**:</span><span class="sxs-lookup"><span data-stu-id="68b1c-983">**Correct**:</span></span>

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

  * <span data-ttu-id="68b1c-984">Évitez d’injecter une fabrique qui résout les dépendances au moment de l’exécution à l’aide de <xref:System.IServiceProvider.GetService*> .</span><span class="sxs-lookup"><span data-stu-id="68b1c-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="68b1c-985">Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="68b1c-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="68b1c-986">Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation.</span><span class="sxs-lookup"><span data-stu-id="68b1c-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="68b1c-987">Les exceptions sont rares et représentent principalement des cas spéciaux dans le framework lui-même.</span><span class="sxs-lookup"><span data-stu-id="68b1c-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="68b1c-988">L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux.</span><span class="sxs-lookup"><span data-stu-id="68b1c-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="68b1c-989">Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.</span><span class="sxs-lookup"><span data-stu-id="68b1c-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68b1c-990">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="68b1c-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="68b1c-991">Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)</span><span class="sxs-lookup"><span data-stu-id="68b1c-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="68b1c-992">Principe des dépendances explicites</span><span class="sxs-lookup"><span data-stu-id="68b1c-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="68b1c-993">Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="68b1c-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="68b1c-994">Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68b1c-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
