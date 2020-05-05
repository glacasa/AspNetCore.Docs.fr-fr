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
ms.openlocfilehash: 742f3c5ea26fab5e168f162a0e133da05fd74a74
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767112"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core l’injection de dépendances éblouissantes

Par [Rainer Stropek](https://www.timecockpit.com) et [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Éblouissant prend en charge l' [injection de dépendances (di)](xref:fundamentals/dependency-injection). Les applications peuvent utiliser des services intégrés en les injectant dans des composants. Les applications peuvent également définir et inscrire des services personnalisés et les rendre disponibles dans toute l’application via DI.

La méthode DI est une technique permettant d’accéder à des services configurés dans un emplacement central. Cela peut être utile dans les applications éblouissantes pour :

* Partager une seule instance d’une classe de service sur de nombreux composants, appelé service *Singleton* .
* Découplez les composants des classes de service concrètes à l’aide d’abstractions de référence. Par exemple, considérez une `IDataAccess` interface pour accéder aux données dans l’application. L’interface est implémentée par une `DataAccess` classe concrète et inscrite en tant que service dans le conteneur de services de l’application. Quand un composant utilise DI pour recevoir une `IDataAccess` implémentation, le composant n’est pas couplé au type concret. L’implémentation peut être permutée, peut-être pour une implémentation factice dans les tests unitaires.

## <a name="default-services"></a>Services par défaut

Les services par défaut sont automatiquement ajoutés à la collection de services de l’application.

| Service | Durée de vie | Description |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Temporaire | Fournit des méthodes pour envoyer des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI.<br><br>L’instance de `HttpClient` dans une application de webassembly éblouissant utilise le navigateur pour gérer le trafic HTTP en arrière-plan.<br><br>Les applications serveur éblouissantes n’incluent `HttpClient` pas une configuration en tant que service par défaut. Fournissez `HttpClient` un à une application de serveur éblouissante.<br><br>Pour plus d’informations, consultez <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (webassembly éblouissant)<br>Étendu (serveur éblouissant) | Représente une instance d’un Runtime JavaScript dans laquelle les appels JavaScript sont distribués. Pour plus d’informations, consultez <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (webassembly éblouissant)<br>Étendu (serveur éblouissant) | Contient des assistances pour l’utilisation des URI et de l’état de navigation. Pour plus d’informations, consultez [URI et assistance de l’état de navigation](xref:blazor/routing#uri-and-navigation-state-helpers). |

Un fournisseur de services personnalisé ne fournit pas automatiquement les services par défaut indiqués dans le tableau. Si vous utilisez un fournisseur de services personnalisé et que vous avez besoin de l’un des services répertoriés dans le tableau, ajoutez les services requis au nouveau fournisseur de services.

## <a name="add-services-to-an-app"></a>Ajouter des services à une application

### <a name="blazor-webassembly"></a>WebAssembly Blazor

Configurez les services de la collection de services `Main` de l’application dans la méthode de *Program.cs*. Dans l’exemple suivant, l' `MyDependency` implémentation est inscrite `IMyDependency`pour :

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

Une fois l’hôte généré, les services sont accessibles à partir de l’étendue de l’injection de comptes racine avant le rendu des composants. Cela peut être utile pour l’exécution de la logique d’initialisation avant le rendu du contenu :

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

L’hôte fournit également une instance de configuration centrale pour l’application. En s’appuyant sur l’exemple précédent, l’URL du service météo est passée d’une source de configuration par défaut (par exemple, `InitializeWeatherAsync` *appSettings. JSON*) à :

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

Après avoir créé une nouvelle application, examinez la `Startup.ConfigureServices` méthode :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Une `ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, qui est une liste d’objets descripteurs de service (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>), est passée à la méthode. Les services sont ajoutés en fournissant des descripteurs de service à la collection de services. L’exemple suivant illustre le concept avec l' `IDataAccess` interface et son implémentation `DataAccess`concrète :

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
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorLes applications webassembly n’ont pas actuellement de concept d’étendues DI. `Scoped`-les services inscrits se `Singleton` comportent comme des services. Toutefois, le Blazor modèle d’hébergement de serveur `Scoped` prend en charge la durée de vie. Dans Blazor les applications serveur, l’inscription d’un service étendu est limitée à la *connexion*. Pour cette raison, il est préférable d’utiliser les services délimités pour les services qui doivent être étendus à l’utilisateur actuel, même si l’objectif actuel est d’exécuter côté client dans le navigateur. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI crée une *seule instance* du service. Tous les composants qui `Singleton` requièrent un service reçoivent une instance du même service. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Chaque fois qu’un composant obtient une instance d' `Transient` un service à partir du conteneur de service, il reçoit une *nouvelle instance* du service. |

Le système DI est basé sur le système DI dans ASP.NET Core. Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Demander un service dans un composant

Une fois les services ajoutés à la collection de services, injectez les services dans les [ \@](xref:mvc/views/razor#inject) composants à l’aide de la directive Inject Razor . `@inject`a deux paramètres :

* Tapez &ndash; le type du service à injecter.
* Propriété &ndash; nom de la propriété qui reçoit le service d’application injecté. La propriété ne nécessite pas de création manuelle. Le compilateur crée la propriété.

Pour plus d’informations, consultez <xref:mvc/views/dependency-injection>.

Utilisez plusieurs `@inject` instructions pour injecter différents services.

L'exemple suivant montre comment utiliser `@inject`. Le service qui `Services.IDataAccess` implémente est injecté dans la propriété `DataRepository`du composant. Notez la manière dont le code utilise uniquement `IDataAccess` l’abstraction :

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

En interne, la propriété générée (`DataRepository`) utilise l' `InjectAttribute` attribut. En règle générale, cet attribut n’est pas utilisé directement. Si une classe de base est requise pour les composants et les propriétés injectées sont également requises pour la classe de base `InjectAttribute`, ajoutez manuellement le :

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Dans les composants dérivés de la classe de `@inject` base, la directive n’est pas obligatoire. Le `InjectAttribute` de la classe de base est suffisant :

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Utiliser DI dans les services

Les services complexes peuvent nécessiter des services supplémentaires. Dans l’exemple précédent, `DataAccess` peut nécessiter `HttpClient` le service par défaut. `@inject`(ou) `InjectAttribute`n’est pas disponible pour une utilisation dans les services. L' *injection de constructeur* doit être utilisée à la place. Les services requis sont ajoutés en ajoutant des paramètres au constructeur du service. Lorsque DI crée le service, il reconnaît les services dont il a besoin dans le constructeur et les fournit en conséquence.

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

Conditions préalables pour l’injection de constructeur :

* Un constructeur doit exister dont les arguments peuvent tous être remplis par DI. Les paramètres supplémentaires non couverts par DI sont autorisés s’ils spécifient des valeurs par défaut.
* Le constructeur applicable doit être *public*.
* Un constructeur applicable doit exister. En cas d’ambiguïté, DI lève une exception.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Classes de composants de base de l’utilitaire pour gérer une étendue DI

Dans ASP.NET Core applications, les services délimités sont généralement étendus à la requête actuelle. Une fois la demande terminée, tous les services délimités ou temporaires sont supprimés par le système DI. Dans Blazor les applications serveur, l’étendue de la demande est valable pendant la durée de la connexion cliente, ce qui peut entraîner des services transitoires et de portée bien plus longs que prévu. Dans Blazor les applications webassembly, les services inscrits avec une durée de vie limitée sont traités comme des singletons, de sorte qu’ils vivent plus longtemps que les services étendus dans les applications de ASP.net Core standard.

Une approche qui limite la durée de vie Blazor d’un service dans les `OwningComponentBase` applications est l’utilisation du type. `OwningComponentBase`est un type abstrait dérivé de `ComponentBase` qui crée une étendue di correspondant à la durée de vie du composant. À l’aide de cette étendue, il est possible d’utiliser l’injection de services avec une durée de vie limitée et de les faire vivre aussi longtemps que le composant. Lorsque le composant est détruit, les services du fournisseur de services étendus du composant sont également supprimés. Cela peut être utile pour les services qui :

* Doit être réutilisé dans un composant, car la durée de vie temporaire n’est pas appropriée.
* Ne doit pas être partagé entre les composants, car la durée de vie Singleton n’est pas appropriée.

Deux versions du `OwningComponentBase` type sont disponibles :

* `OwningComponentBase`est un enfant abstrait et jetable du `ComponentBase` type avec une propriété protégée `ScopedServices` de type. `IServiceProvider` Ce fournisseur peut être utilisé pour résoudre les services dont la portée est limitée à la durée de vie du composant.

  Les services d’injection de services injectés `@inject` dans le `InjectAttribute` composant`[Inject]`à l’aide de ou () ne sont pas créés dans l’étendue du composant. Pour utiliser l’étendue du composant, les services doivent être résolus à l’aide `ScopedServices.GetRequiredService` de ou `ScopedServices.GetService`de. Les dépendances de tous `ScopedServices` les services résolus à l’aide du fournisseur sont fournies à partir de cette même étendue.

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

* `OwningComponentBase<T>`dérive de `OwningComponentBase` et ajoute une propriété `Service` qui retourne une instance de `T` à partir du fournisseur di étendu. Ce type est un moyen pratique d’accéder aux services délimités sans utiliser une `IServiceProvider` instance de lorsqu’un service principal est requis par l’application à partir du conteneur di à l’aide de l’étendue du composant. La `ScopedServices` propriété est disponible. par conséquent, l’application peut récupérer des services d’autres types, si nécessaire.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Utilisation de l’Entity Framework DbContext à partir de DI

Un type de service commun à récupérer à partir de DI dans Web Apps est Entity Framework `DbContext` les objets (EF). L’inscription des services `IServiceCollection.AddDbContext` EF à `DbContext` l’aide de ajoute en tant que service étendu par défaut. L’inscription en tant que service étendu peut entraîner des problèmes Blazor dans les applications, `DbContext` car elle entraîne une longue durée de vie des instances et leur partage sur l’ensemble de l’application. `DbContext`n’est pas thread-safe et ne doit pas être utilisé simultanément.

Selon l’application, l’utilisation `OwningComponentBase` de pour limiter l’étendue d’un `DbContext` à un seul composant *peut* résoudre le problème. Si un composant n’utilise pas `DbContext` en parallèle, le fait de dériver `OwningComponentBase` le composant de et `DbContext` de `ScopedServices` récupérer le de est suffisant, car il garantit que :

* Les composants distincts ne partagent `DbContext`pas un.
* La `DbContext` fonction vit uniquement tant que le composant en dépend.

Si un seul composant peut utiliser un `DbContext` en même temps (par exemple, chaque fois qu’un utilisateur sélectionne un bouton), même `OwningComponentBase` l’utilisation de n’évite pas les problèmes liés à des opérations EF simultanées. Dans ce cas, utilisez un différent `DbContext` pour chaque opération EF logique. Utilisez l’une des approches suivantes :

* Créez `DbContext` directement à l' `DbContextOptions<TContext>` aide de comme argument, qui peut être récupéré à partir de di et est thread-safe.

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

* Inscrivez le `DbContext` dans le conteneur de service avec une durée de vie transitoire :
  * Lors de l’inscription du contexte `ServiceLifetime.Transient`, utilisez. La `AddDbContext` méthode d’extension accepte deux paramètres facultatifs `ServiceLifetime`de type. Pour utiliser cette approche, seul le `contextLifetime` paramètre doit être `ServiceLifetime.Transient`. `optionsLifetime`peut conserver sa valeur par défaut `ServiceLifetime.Scoped`.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Le temporaire `DbContext` peut être injecté comme normal (à l' `@inject`aide de) dans des composants qui n’exécuteront pas plusieurs opérations EF en parallèle. Ceux qui peuvent exécuter plusieurs opérations EF simultanément peuvent demander des `DbContext` objets distincts pour chaque opération parallèle `IServiceProvider.GetRequiredService`à l’aide de.

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
