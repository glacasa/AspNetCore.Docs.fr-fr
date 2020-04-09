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
# <a name="aspnet-core-blazor-dependency-injection"></a>injection de dépendance ASP.NET Core Blazor

Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor soutient [l’injection de dépendance (DI)](xref:fundamentals/dependency-injection). Les applications peuvent utiliser des services intégrés en les injectant dans des composants. Les applications peuvent également définir et enregistrer des services personnalisés et les rendre disponibles dans toute l’application via DI.

DI est une technique d’accès aux services configurés dans un emplacement central. Cela peut être utile dans les applications Blazor pour :

* Partagez une seule instance d’une classe de service sur de nombreux composants, connu sous le nom de service *singleton.*
* Découpler les composants des classes de service en béton en utilisant des abstractions de référence. Par exemple, envisagez une interface `IDataAccess` pour accéder aux données de l’application. L’interface est implémentée par une classe concrète `DataAccess` et enregistrée comme service dans le conteneur de service de l’application. Lorsqu’un composant utilise `IDataAccess` DI pour recevoir une implémentation, le composant n’est pas couplé au type concret. La mise en œuvre peut être échangée, peut-être pour une mise en œuvre simulée dans les tests unitaires.

## <a name="default-services"></a>Services par défaut

Les services par défaut sont automatiquement ajoutés à la collecte de services de l’application.

| Service | Durée de vie | Description |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Fournit des méthodes pour envoyer des demandes de HTTP et recevoir des réponses HTTP à partir d’une ressource identifiée par un URI.<br><br>L’exemple `HttpClient` d’une application Blazor WebAssembly utilise le navigateur pour gérer le trafic HTTP en arrière-plan.<br><br>Les applications Blazor Server `HttpClient` n’incluent pas un service configuré par défaut. Fournir `HttpClient` une application Blazor Server.<br><br>Pour plus d’informations, consultez <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Portée (Blazor Server) | Représente une instance d’un temps d’exécution JavaScript où les appels JavaScript sont envoyés. Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Portée (Blazor Server) | Contient des aides pour travailler avec les ITI et l’état de navigation. Pour plus d’informations, voir [URI et les aides d’état de navigation](xref:blazor/routing#uri-and-navigation-state-helpers). |

Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut répertoriés dans le tableau. Si vous utilisez un fournisseur de services personnalisés et que vous avez besoin de l’un des services indiqués dans le tableau, ajoutez les services requis au nouveau fournisseur de services.

## <a name="add-services-to-an-app"></a>Ajouter des services à une application

### <a name="blazor-webassembly"></a>WebAssembly Blazor

Configurer les services pour la `Main` collection de services de l’application dans la méthode de *Program.cs*. Dans l’exemple `MyDependency` suivant, la `IMyDependency`mise en œuvre est enregistrée pour :

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

Une fois l’hôte construit, les services peuvent être consultés à partir de la portée DI racine avant que tous les composants soient rendus. Cela peut être utile pour exécuter la logique de initialisation avant de rendre le contenu:

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

L’hôte fournit également une instance de configuration centrale pour l’application. S’appuyant sur l’exemple précédent, l’URL du service météorologique est transmise d’une source `InitializeWeatherAsync`de configuration par défaut (par exemple, *appsettings.json*) à :

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

### <a name="blazor-server"></a>Serveur Blazor

Après la création d’une nouvelle application, examinez la `Startup.ConfigureServices` méthode :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

La `ConfigureServices` méthode est <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>adoptée un , qui est une<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>liste d’objets descripteur de service ( ). Les services sont ajoutés en fournissant des descripteurs de service à la collection de services. L’exemple suivant montre le `IDataAccess` concept avec `DataAccess`l’interface et sa mise en œuvre concrète :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Durée de vie du service

Les services peuvent être configurés avec les durées de vie indiquées dans le tableau suivant.

| Durée de vie | Description |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorLes applications WebAssembly n’ont pas actuellement de concept de portée DI. `Scoped`les services enregistrés `Singleton` se comportent comme des services. Cependant, Blazor le modèle d’hébergement Server prend en charge la `Scoped` durée de vie. Dans Blazor les applications Server, une inscription de service étendue est étendue à la *connexion*. Pour cette raison, l’utilisation de services à portée est préférable pour les services qui devraient être étendues à l’utilisateur actuel, même si l’intention actuelle est d’exécuter le côté client dans le navigateur. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI crée une *seule instance* du service. Tous les composants `Singleton` nécessitant un service reçoivent une instance du même service. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Chaque fois qu’un composant `Transient` obtient une instance d’un service à partir du conteneur de service, il reçoit une *nouvelle instance* du service. |

Le système DI est basé sur le système DI dans ASP.NET Core. Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Demander un service dans un composant

Une fois que les services sont ajoutés à la [ \@](xref:mvc/views/razor#inject) collecte de services, injectez les services dans les composants à l’aide de la directive Razor injectable. `@inject`a deux paramètres :

* Tapez &ndash; le type de service à injecter.
* Propriété &ndash; Le nom de la propriété recevant le service d’application injecté. La propriété ne nécessite pas de création manuelle. Le compilateur crée la propriété.

Pour plus d’informations, consultez <xref:mvc/views/dependency-injection>.

Utilisez `@inject` plusieurs instructions pour injecter différents services.

L'exemple suivant montre comment utiliser `@inject`. La mise `Services.IDataAccess` en œuvre du `DataRepository`service est injectée dans la propriété du composant. Notez comment le code `IDataAccess` utilise uniquement l’abstraction :

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

En interne, la`DataRepository`propriété `InjectAttribute` générée ( ) utilise l’attribut. Typiquement, cet attribut n’est pas utilisé directement. Si une classe de base est nécessaire pour les composants et les `InjectAttribute`propriétés injectées sont également nécessaires pour la classe de base, ajouter manuellement le :

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Dans les composants dérivés de `@inject` la classe de base, la directive n’est pas requise. La `InjectAttribute` classe de base est suffisante :

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Utiliser DI dans les services

Les services complexes peuvent nécessiter des services supplémentaires. Dans l’exemple `DataAccess` précédent, `HttpClient` peut nécessiter le service par défaut. `@inject`(ou `InjectAttribute`le ) n’est pas disponible pour une utilisation dans les services. *L’injection de constructeur* doit être utilisée à la place. Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service. Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.

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

Conditions préalables à l’injection de constructeurs :

* Un constructeur doit exister dont les arguments peuvent tous être remplis par DI. Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient les valeurs par défaut.
* Le constructeur applicable doit être *public*.
* Un constructeur applicable doit exister. En cas d’ambiguïté, DI jette une exception.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de composants de base d’utilité pour gérer une portée DI

Dans ASP.NET applications Core, les services à portée sont généralement étendues à la demande actuelle. Une fois la demande terminée, tout service d’envergure ou de passage est éliminé par le système DI. Dans Blazor les applications Server, la portée de la demande dure pendant la durée de la connexion client, ce qui peut entraîner des services transitoires et d’envergure qui vivent beaucoup plus longtemps que prévu. Dans Blazor les applications WebAssembly, les services enregistrés avec une durée de vie étendue sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services de portée dans les applications ASP.NET core typiques.

Une approche qui limite Blazor la durée de `OwningComponentBase` vie d’un service dans les applications est l’utilisation du type. `OwningComponentBase`est un type `ComponentBase` abstrait dérivé de celui qui crée une portée DI correspondant à la durée de vie du composant. En utilisant cette portée, il est possible d’utiliser des services DI avec une durée de vie étendue et de les faire vivre aussi longtemps que le composant. Lorsque le composant est détruit, les services du fournisseur de services à portée du composant sont également éliminés. Cela peut être utile pour les services qui :

* Devrait être réutilisé dans un composant, car la durée de vie transitoire est inappropriée.
* Ne devrait pas être partagé entre les composants, car la durée de vie du singleton est inappropriée.

Deux versions `OwningComponentBase` du type sont disponibles :

* `OwningComponentBase`est un enfant abstrait `ComponentBase` et jetable `ScopedServices` du `IServiceProvider`type avec une propriété protégée de type . Ce fournisseur peut être utilisé pour résoudre les services qui sont étendues à la durée de vie du composant.

  Les services d' `@inject` DI `InjectAttribute` `[Inject]`injectés dans le composant à l’aide ou le ( ) ne sont pas créés dans la portée du composant. Pour utiliser la portée du composant, `ScopedServices.GetRequiredService` les `ScopedServices.GetService`services doivent être résolus à l’aide ou . Tous les services `ScopedServices` résolus à l’aide du fournisseur ont leurs dépendances fournies à partir de cette même portée.

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

* `OwningComponentBase<T>`tire et `OwningComponentBase` ajoute une `Service` propriété qui `T` renvoie une instance du fournisseur DE DI à portée. Ce type est un moyen pratique d’accéder `IServiceProvider` aux services d’envergure sans utiliser un exemple de lorsqu’il y a un service principal que l’application nécessite à partir du conteneur DI en utilisant la portée du composant. La `ScopedServices` propriété est disponible, de sorte que l’application peut obtenir des services d’autres types, si nécessaire.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Utilisation de l’entité Framework DbContext de DI

Un type de service commun à récupérer à partir `DbContext` de DI dans les applications Web est Entity Framework (EF) objets. L’enregistrement des `IServiceCollection.AddDbContext` services `DbContext` EF en utilisant ajoute le service comme un service d’envergure par défaut. L’inscription en tant que service Blazor à portée `DbContext` peut entraîner des problèmes dans les applications, car elle entraîne des cas à longue durée de vie et partagés sur l’application. `DbContext`n’est pas sans fil et ne doit pas être utilisé simultanément.

Selon l’application, `OwningComponentBase` l’utilisation pour `DbContext` limiter la portée d’un composant à un seul composant *peut* résoudre le problème. Si un composant n’utilise `DbContext` pas un en parallèle, le fait de dérimer le composant et de `OwningComponentBase` la `DbContext` récupérer `ScopedServices` est suffisant parce qu’il garantit que :

* Les composants distincts ne `DbContext`partagent pas un .
* La `DbContext` vie seulement aussi longtemps que le composant en fonction de lui.

Si un seul composant `DbContext` peut utiliser un composant en même temps (par `OwningComponentBase` exemple, chaque fois qu’un utilisateur sélectionne un bouton), même en utilisant n’évite pas les problèmes avec les opérations EF simultanées. Dans ce cas, `DbContext` utilisez un autre pour chaque opération EF logique. Utilisez l’une ou l’autre des approches suivantes :

* Créez `DbContext` l’utilisation `DbContextOptions<TContext>` directe comme argument, qui peut être récupéré à partir de DI et est sans danger de thread.

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

* Enregistrez `DbContext` le conteneur dans le conteneur de service avec une durée de vie transitoire :
  * Lors de l’enregistrement `ServiceLifetime.Transient`du contexte, utilisez . La `AddDbContext` méthode d’extension prend `ServiceLifetime`deux paramètres facultatifs de type . Pour utiliser cette approche, seul `contextLifetime` `ServiceLifetime.Transient`le paramètre doit être . `optionsLifetime`peut conserver sa `ServiceLifetime.Scoped`valeur par défaut de .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Le transitoire `DbContext` peut être injecté `@inject`comme d’habitude (en utilisant) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle. Ceux qui peuvent effectuer plusieurs opérations `DbContext` EF simultanément peuvent `IServiceProvider.GetRequiredService`demander des objets distincts pour chaque opération parallèle à l’aide de .

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

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
