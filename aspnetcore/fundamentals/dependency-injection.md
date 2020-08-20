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
# <a name="dependency-injection-in-aspnet-core"></a>Injection de dépendances dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.

Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.

Pour plus d’informations sur l’injection de dépendances d’options, consultez <xref:fundamentals/configuration/options> .

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Vue d’ensemble de l’injection de dépendances

Une *dépendance* est un objet qui nécessite un autre objet. Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :

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

Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe. La classe `MyDependency` est une dépendance de la classe `IndexModel` :

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

La classe est créee et dépend directement de l’instance `MyDependency`. Les dépendances de code, telles que l’exemple précédent, sont problématiques et doivent être évitées pour les raisons suivantes :

* Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.
* Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe. Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.
* Cette implémentation complique le test unitaire. L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.

L’injection de dépendances résout ces problèmes via :

* L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.
* L’inscription de la dépendance dans un conteneur de service. ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>. Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.
* *Injection* du service dans le constructeur de la classe où il est utilisé. Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.

Dans l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Cette interface est implémentée par un type concret, `MyDependency` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`. La <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> méthode enregistre le service avec une durée de vie limitée, la durée de vie d’une requête unique. Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

Avec le modèle DI :

* Le contrôleur n’utilise pas le type concret `MyDependency` , mais uniquement l’interface `IMyDependency` . Cela facilite la modification de l’implémentation que le contrôleur utilise sans modifier le contrôleur.
* Le contrôleur ne crée pas d’instance de `MyDependency` , il est créé par le conteneur di.

L’implémentation de l' `IMyDependency` interface peut être améliorée à l’aide de l’API de journalisation intégrée :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

La méthode mise à jour `ConfigureServices` inscrit la nouvelle `IMyDependency` implémentation :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2` demande un <xref:Microsoft.Extensions.Logging.ILogger`1> dans le constructeur. Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée. Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances. Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu. L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.

`ILogger<TCategoryName>` est un [service fourni par le Framework](#framework-provided-services).

Le conteneur se résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui évite d’avoir à inscrire chaque [type construit (Générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types).

Dans la terminologie d’injection de dépendances, un service :

* Est généralement un objet qui fournit un service à un autre code de l’application, tel que le `IMyDependency` service.
* N’est pas associé à un service Web, bien que le service puisse utiliser un service Web.

Le Framework fournit un système de [journalisation](xref:fundamentals/logging/index) robuste. Les `IMyDependency` implémentations ont été écrites pour illustrer la di de base, et non l’implémentation de la journalisation. La plupart des applications n’ont pas besoin d’écrire des enregistreurs. Le code suivant illustre l’utilisation de la journalisation par défaut, qui ne nécessite pas l’inscription de services dans `ConfigureServices` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

À l’aide du code précédent, il n’est pas nécessaire de mettre à jour, `ConfigureServices` car la [journalisation](xref:fundamentals/logging/index) est fournie par le Framework :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a>Services injectés au démarrage

Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Les services peuvent être injectés dans `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Pour plus d’informations, consultez <xref:fundamentals/startup> et [configuration de l’accès au démarrage](xref:fundamentals/configuration/index#access-configuration-in-startup).

## <a name="register-additional-services-with-extension-methods"></a>Inscrire des services supplémentaires avec les méthodes d’extension

Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service :

* La Convention consiste à utiliser une seule `Add{SERVICE_NAME}` méthode d’extension pour inscrire tous les services requis par ce service.
* Les services dépendants sont également inscrits.

Le code suivant est généré par le Razor modèle pages à l’aide de comptes d’utilisateur individuels et montre comment ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

Pour plus d’informations, consultez <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> et <xref:security/authentication/identity>.

Consultez la section [combinaison](#csc) de la collection de services pour obtenir des instructions sur l’écriture d’une méthode d’extension pour inscrire des services.

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>Durées de service

Choisissez une durée de vie appropriée pour chaque service inscrit. Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :

### <a name="transient"></a>Temporaire

Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service. Cette durée de vie convient parfaitement aux services légers et sans état.

Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.

### <a name="scoped"></a>Délimité

Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).

Lors de l’utilisation de Entity Framework Core, la <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> méthode d’extension inscrit les `DbContext` types avec une durée de vie limitée par défaut.

Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.

Utilisez les services délimités dans l’intergiciel (middleware) avec l’une des approches suivantes :

* Injectez le service dans `Invoke` la `InvokeAsync` méthode ou. L’injection par [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) lève une exception au moment de l’exécution, car elle force le service à se comporter comme un singleton. L’exemple dans les [options de durée de vie et d’inscription](#lifetime-and-registration-options) utilise l' `InvokeAsync` approche.
* [Intergiciel (middleware) basé sur l’usine](<xref:fundamentals/middleware/extensibility>). Les méthodes d’extension de <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> vérifient si le type inscrit d’un middleware implémente <xref:Microsoft.AspNetCore.Http.IMiddleware>. Si c’est le cas, l’instance de <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> inscrite dans le conteneur est utilisée pour résoudre l’implémentation de <xref:Microsoft.AspNetCore.Http.IMiddleware>, au lieu de la logique d’activation de middleware basée sur une convention. Le middleware est inscrit comme service délimité ou temporaire dans le conteneur de service de l’application.

Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

Ne résolvez ***pas*** un service étendu à partir d’un singleton. L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes. Il convient d’effectuer les opérations suivantes :

* Résoudre un service Singleton à partir d’un service délimité ou étendu.
* Résoudre un service étendu à partir d’un autre service étendu ou temporaire.

Par défaut, dans l’environnement de développement, la résolution d’un service à partir d’un autre service avec une durée de vie plus longue lève une exception. Pour plus d’informations, consultez [Validation de l’étendue](#sv).

### <a name="singleton"></a>Singleton

Les services de durée de vie Singleton ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) sont créés :

* La première fois qu’ils sont demandés.
* Par le développeur, lors de la fourniture d’une instance d’implémentation directement au conteneur. Cette approche est rarement nécessaire.

Chaque requête ultérieure utilise la même instance. Si l’application requiert un comportement Singleton, autorisez le conteneur de service à gérer la durée de vie du service. N’implémentez pas le modèle de conception Singleton et fournissez du code pour supprimer le singleton. Les services ne doivent jamais être supprimés par du code qui a résolu le service à partir d’un conteneur. Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprimera automatiquement le singleton.

Les services Singleton doivent être thread-safe et sont souvent utilisés dans les services sans État.

Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé lors de l’arrêt de l’application. Étant donné que la mémoire n’est pas libérée tant que l’application n’est pas arrêtée, l’utilisation de la mémoire avec un singleton doit être prise en compte.

> [!WARNING]
> Ne résolvez ***pas*** un service étendu à partir d’un singleton. L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes. Il est parfait de résoudre un service Singleton à partir d’un service étendu ou temporaire.

## <a name="service-registration-methods"></a>Méthodes d’inscription du service

Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| Méthode | Automatique<br>object<br>suppression | Multiple<br>implémentations | Passage d’args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Exemple :<br>`services.AddSingleton<IMyDep, MyDep>();` | Oui | Oui | Non |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Exemples :<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | Oui | Oui | Oui |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Exemple :<br>`services.AddSingleton<MyDep>();` | Oui | Non | Non |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Exemples :<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | Non | Oui | Oui |
| `AddSingleton(new {IMPLEMENTATION})`<br>Exemples :<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | Non | Non | Oui |

Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services). Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}` les méthodes inscrivent le service si aucune implémentation n’est déjà inscrite.

Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`. La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Pour plus d'informations, consultez les pages suivantes :

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Les méthodes [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent le service s’il n’existe pas déjà une implémentation *du même type*. Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`. Lors de l’inscription des services, le développeur doit ajouter une instance si l’un des mêmes types n’a pas déjà été ajouté. En général, les auteurs de bibliothèque utilisent `TryAddEnumerable` pour éviter d’inscrire plusieurs copies d’une implémentation dans le conteneur.

Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`. La deuxième ligne inscrit `MyDep` pour `IMyDep2`. La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

L’inscription de service est généralement indépendante de l’ordre, sauf lors de l’inscription de plusieurs implémentations du même type.

`IServiceCollection` est une collection de <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> . Le code suivant montre comment ajouter un service à l’aide d’un constructeur :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

Les `Add{LIFETIME}` méthodes utilisent la même approche. Par exemple, consultez le [code source pour AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).

### <a name="constructor-injection-behavior"></a>Comportement d’injection de constructeurs

Les services peuvent être résolus par deux mécanismes :

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:
  * Crée des objets sans inscription du service dans le conteneur d’injection de dépendances.
  * Utilisé avec les fonctionnalités d’infrastructure, telles que les [balises d’assistance](xref:mvc/views/tag-helpers/intro), les contrôleurs MVC et les [classeurs de modèles](xref:mvc/models/model-binding).

Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.

Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .

Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable. Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.

## <a name="entity-framework-contexts"></a>Contextes Entity Framework

Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client. La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données. Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.

## <a name="lifetime-and-registration-options"></a>Options de durée de vie et d’inscription

Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches comme une opération avec un identificateur, `OperationId` . Selon la configuration de la durée de vie du service d’une opération pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’elle est demandée par une classe :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Les interfaces sont implémentées dans la classe `Operation`. Le `Operation` constructeur génère les 4 derniers caractères d’un GUID si aucun n’est fourni :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

L’exemple d’application montre les durées de vie des objets dans et entre les demandes. L’exemple d’application `IndexModel` et l’intergiciel (middleware) demandent chaque `IOperation` type de type et journalisent les `OperationId` éléments suivants :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

L’intergiciel est semblable à `IndexModel` et résout les mêmes services :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

Les services délimités doivent être résolus dans la `InvokeAsync` méthode :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

La sortie de l’enregistreur d’événements affiche :

* Les objets *Transient* sont toujours différents. La `OperationId` valeur transitoire est différente dans le `IndexModel` et l’intergiciel (middleware).
* Les objets *délimités* sont les mêmes dans chaque demande, mais différents dans chaque demande.
* Les objets *Singleton* sont les mêmes pour chaque requête.

Pour réduire la sortie de journalisation, définissez « journalisation : LogLevel : Microsoft : erreur » dans le *appsettings.Development.jssur* le fichier :

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>Appeler des services à partir de Main

Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application. Cette approche est utile pour accéder à un service étendu au démarrage pour exécuter des tâches d’initialisation :

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

Le code précédent consiste à [Ajouter l’initialiseur de valeur initiale](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) dans le didacticiel sur les Razor pages.

L’exemple suivant montre comment obtenir un contexte pour `IMyDependency` dans `Program.Main` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>Validation de l’étendue

Lorsque l’application s’exécute dans l' [environnement de développement](xref:fundamentals/environments) et appelle [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) pour générer l’hôte, le fournisseur de services par défaut effectue des vérifications pour vérifier que :

* Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.
* Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.
* Les services temporaires ne sont pas injectés directement ou indirectement dans des singletons ou des services délimités.

Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé. La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application lorsque le fournisseur démarre avec l’application et est supprimée lorsque l’application s’arrête.

Les services Scoped sont supprimés par le conteneur qui les a créés. Si un service étendu est créé dans le conteneur racine, la durée de vie du service est effectivement promue en Singleton, car il est supprimé uniquement par le conteneur racine lorsque l’application est arrêtée. La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.

Pour plus d’informations, consultez [Validation de l’étendue](xref:fundamentals/host/web-host#scope-validation).

## <a name="request-services"></a>Services de requête

Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Les services de requête représentent les services configurés et demandés dans le cadre de l’application. Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.

En règle générale, l’application ne doit pas utiliser directement ces propriétés. Au lieu de cela, demandez les types que les classes requièrent via les constructeurs de classe et autorisez l’infrastructure à injecter les dépendances. Cela génère des classes qui sont plus faciles à tester.

ASP.NET Core crée une étendue par demande et `RequestServices` expose le fournisseur de services étendus. Tous les services délimités sont valides tant que la demande est active.

> [!NOTE]
> Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Conception de services pour l’injection de dépendances

Les bonnes pratiques permettent de :

* Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.
* Évitez les classes et les membres avec état, static. Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.
* Éviter une instanciation directe de classes dépendantes au sein de services. L’instanciation directe associe le code à une implémentation particulière.
* Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.

Si une classe semble avoir trop de dépendances injectées, il s’agit généralement d’un signe que la classe a un trop grand nombre de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe. Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur.

### <a name="disposal-of-services"></a>Suppression des services

Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée. Les services ne doivent jamais être supprimés par un code qui a résolu le service à partir d’un conteneur. Si un type ou une fabrique est inscrit en tant que singleton, le conteneur supprime le singleton.

Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

La console de débogage affiche la sortie suivante après chaque actualisation de la page d’index :

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>Services non créés par le conteneur de service
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
Considérez le code suivant :

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

Dans le code précédent :

* Les instances de service ne sont pas créées par le conteneur de service.
* Les durées de vie de service prévues ne sont pas connues de l’infrastructure.
* L’infrastructure ne supprime pas automatiquement les services.
* Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Recommandations IDisposable pour les instances temporaires et partagées

#### <a name="transient-limited-lifetime"></a>Transitoire, durée de vie limitée

**Scénario**

L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :

* L’instance est résolue dans l’étendue racine (conteneur racine).
* L’instance doit être supprimée avant la fin de l’étendue.

**Solution**

Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente. Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final. Si le type final a d’autres dépendances, la fabrique peut :

* Recevoir un <xref:System.IServiceProvider> dans son constructeur.
* Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.

#### <a name="shared-instance-limited-lifetime"></a>Instance partagée, durée de vie limitée

**Scénario**

L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.

**Solution**

Inscrivez l’instance avec une durée de vie limitée. Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis. Supprimez l’étendue lorsque la durée de vie doit se terminer.

#### <a name="general-idisposable-guidelines"></a>Instructions IDisposable générales

* N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire. Utilisez à la place le modèle de fabrique.
* Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine. La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.
* La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même. Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.
* Les portées doivent être utilisées pour contrôler les durées de vie des services. Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.

## <a name="default-service-container-replacement"></a>Remplacement de conteneur de services par défaut

Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public. Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :

* Injection de propriétés
* Injection en fonction du nom
* Conteneurs enfants
* Gestion personnalisée de la durée de vie
* `Func<T>` prend en charge l’initialisation tardive
* Inscription basée sur une convention

Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Sécurité des threads

Créez des services singleton thread-safe. Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.

La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe. Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.

## <a name="recommendations"></a>Recommandations

* La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge. C# ne prend pas en charge les constructeurs asynchrones. Le modèle recommandé consiste à utiliser des méthodes asynchrones après la résolution synchrone du service.
* Évitez de stocker des données et des configurations directement dans le conteneur de services. Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services. La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options). De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet. Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.
* Évitez l’accès statique aux services. Par exemple, évitez de taper statiquement [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pour une utilisation ailleurs).
* Conservez les fabriques DI en mode rapide et synchrone.
* Évitez d’utiliser le *modèle de localisation de service*. Par exemple, n’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service si vous pouvez utiliser l’injection de dépendance à la place :

  **Incorrect :**

    ![Code incorrect](dependency-injection/_static/bad.png)

  **Correct**:

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
* Une autre variante du localisateur de service à éviter est l’injection d’une fabrique qui résout les dépendances au moment de l’exécution. Ces deux pratiques combinent des stratégies [Inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).
* Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

<a name="ASP0000"></a>
* Évitez les appels à <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> dans `ConfigureServices` . L’appel de `BuildServiceProvider` se produit généralement lorsque le développeur souhaite résoudre un service dans `ConfigureServices` . Par exemple, considérez le cas où vous devez obtenir le `LoginPath` de la configuration de. Évitez le code suivant :

  ![code incorrect appelant BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  Dans l’image précédente, la sélection de la ligne ondulée verte sous `services.BuildServiceProvider` affiche l’avertissement ASP0000 suivant :
    * ASP0000 l’appel de’BuildServiceProvider’à partir du code de l’application entraîne la création d’une copie supplémentaire des services Singleton. Envisagez des alternatives telles que la dépendance injectant des services en tant que paramètres pour « configurer ».

   L’appel `BuildServiceProvider` de crée un deuxième conteneur, qui peut créer des singletons endommagés et provoquer des références aux graphiques d’objets sur plusieurs conteneurs. Une méthode correcte pour obtenir `LoginPath` est d’utiliser le modèle d’option avec di :

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* Les services transitoires jetables sont capturés par le conteneur pour la suppression. Cela peut entraîner une fuite de mémoire si elle est résolue à partir du conteneur de niveau supérieur.
* Activez la validation d’étendue pour vous assurer que l’application n’a pas de services délimités capturant les singletons. Pour plus d’informations, consultez [Validation de l’étendue](#scope-validation).

Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation. Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.

L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux. Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Modèles recommandés pour l’architecture mutualisée dans DI

Le [cœur du verger](https://github.com/OrchardCMS/OrchardCore) fournit une architecture mutualisée. Pour plus d’informations, consultez la documentation sur le [noyau du verger](https://docs.orchardcore.net/en/dev/).

Consultez les exemples d’applications à l’adresse https://github.com/OrchardCMS/OrchardCore.Samples pour obtenir des exemples de création d’applications modulaires et mutualisées à l’aide de l’infrastructure principale du verger uniquement sans les fonctionnalités spécifiques au CMS.

## <a name="framework-provided-services"></a>Services fournis par le framework

La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc. Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host). Les applications basées sur un ASP.NET Core modèles ont plus de 250 services inscrits par le Framework. Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.

| Type de service | Durée de vie |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="additional-resources"></a>Ressources complémentaires

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [Modèles de conférence norvégiens pour le développement d’applications DI](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Principe des dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)et [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core prend en charge le modèle de conception logicielle d’injection de dépendances, technique qui permet d’obtenir une [inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre les classes et leurs dépendances.

Pour plus d’informations spécifiques à l’injection de dépendances au sein des contrôleurs MVC, consultez <xref:mvc/controllers/dependency-injection>.

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Vue d’ensemble de l’injection de dépendances

Une *dépendance* est un objet qui nécessite un autre objet. Examinez la classe `MyDependency` suivante avec une méthode `WriteMessage` dont dépendent d’autres classes dans une application :

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

Une instance de la classe `MyDependency` peut être créée pour rendre la méthode `WriteMessage` disponible pour une classe. La classe `MyDependency` est une dépendance de la classe `IndexModel` :

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

La classe est créee et dépend directement de l’instance `MyDependency`. Les dépendances de code (comme l’exemple précédent) posent problème et doivent être évitées pour les raisons suivantes :

* Pour remplacer `MyDependency` par une autre implémentation, la classe doit être modifiée.
* Si `MyDependency` possède des dépendances, elles doivent être configurées par la classe. Dans un grand projet comportant plusieurs classes dépendant de `MyDependency`, le code de configuration est disséminé dans l’application.
* Cette implémentation complique le test unitaire. L’application doit utiliser une classe `MyDependency` fictive ou stub, ce qui est impossible avec cette approche.

L’injection de dépendances résout ces problèmes via :

* L’utilisation d’une interface ou classe de base pour extraire l’implémentation des dépendances.
* L’inscription de la dépendance dans un conteneur de service. ASP.NET Core fournit un conteneur de service intégré, <xref:System.IServiceProvider>. Les services sont inscrits dans la méthode `Startup.ConfigureServices` de l’application.
* *Injection* du service dans le constructeur de la classe où il est utilisé. Le framework prend la responsabilité de la création d’une instance de la dépendance et de sa suppression lorsqu’elle n’est plus nécessaire.

Dans l’[exemple d’application](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l’interface `IMyDependency` définit une méthode que le service fournit à l’application :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Cette interface est implémentée par un type concret, `MyDependency` :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` exige un <xref:Microsoft.Extensions.Logging.ILogger`1> dans son constructeur. Il n’est pas rare que l’injection de dépendances soit utilisée de manière chaînée. Dans ce cas, chaque dépendance demandée demande à son tour ses propres dépendances. Le conteneur résout les dépendances dans le graphique et retourne le service entièrement résolu. L’ensemble collectif de dépendances qui doivent être résolues est généralement appelé *arborescence des dépendances*, *graphique de dépendance* ou *graphique d’objet*.

`IMyDependency` et `ILogger<TCategoryName>` doivent être inscrits dans le conteneur de service. `IMyDependency` est inscrit dans `Startup.ConfigureServices`. `ILogger<TCategoryName>` est inscrit par l’infrastructure d’abstractions de journalisation. Il s’agit donc d’un [service fourni par le framework](#framework-provided-services) et inscrit par défaut par l’infrastructure.

Le conteneur résout `ILogger<TCategoryName>` en tirant parti de [types ouverts (génériques)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), ce qui élimine la nécessité d’inscrire chaque [type construit (générique)](/dotnet/csharp/language-reference/language-specification/types#constructed-types) :

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

Dans l’exemple d’application, le service `IMyDependency` est inscrit avec le type concret `MyDependency`. L’inscription ajuste la durée de vie du service à la durée de vie d’une requête unique. Les [durées de vie du service](#service-lifetimes) sont décrites plus loin dans cette rubrique.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Chaque méthode d’extension `services.Add{SERVICE_NAME}` ajoute (et éventuellement configure) des services. Par exemple, `services.AddMvc()` ajoute les pages de services Razor et MVC requièrent. Il est recommandé que les applications suivent cette convention. Placez les méthodes d’extension dans l’espace de noms [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pour encapsuler des groupes d’inscriptions de service.

Si le constructeur du service exige un [type intégré](/dotnet/csharp/language-reference/keywords/built-in-types-table), comme un `string`, le type peut être injecté à l’aide de la [configuration](xref:fundamentals/configuration/index) ou du [modèle d’options](xref:fundamentals/configuration/options) :

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

Une instance du service est demandée via le constructeur d’une classe dans laquelle le service est utilisé et assigné à un champ privé. Le champ est utilisé pour accéder au service en fonction des besoins tout au long de la classe.

Dans l’exemple d’application, l’instance `IMyDependency` est demandée et utilisée pour appeler la méthode `WriteMessage` du service :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Services injectés au démarrage

Seuls les types de service suivants peuvent être injectés dans le `Startup` constructeur lors de l’utilisation de l’hôte générique ( <xref:Microsoft.Extensions.Hosting.IHostBuilder> ) :

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Les services peuvent être injectés dans `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Pour plus d'informations, consultez <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Services fournis par le framework

La `Startup.ConfigureServices` méthode est chargée de définir les services utilisés par l’application, y compris les fonctionnalités de plateforme, telles que Entity Framework Core et ASP.net Core Mvc. Initialement, le `IServiceCollection` fourni pour `ConfigureServices` possède des services définis par l’infrastructure en fonction de la [façon dont l’hôte a été configuré](xref:fundamentals/index#host). Il n’est pas rare qu’une application basée sur un modèle de ASP.NET Core dispose de centaines de services enregistrés par l’infrastructure. Un petit exemple de services inscrits au Framework est répertorié dans le tableau suivant.

| Type de service | Durée de vie |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Temporaire |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Inscrire des services supplémentaires avec les méthodes d’extension

Lorsqu’une méthode d’extension de collection de services est disponible pour inscrire un service (et ses services dépendants, si nécessaire), la convention consiste à utiliser une seule méthode d’extension `Add{SERVICE_NAME}` pour inscrire tous les services requis par ce service. Le code suivant est un exemple de la façon d’ajouter des services supplémentaires au conteneur à l’aide des méthodes d’extension [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) et <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :

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

Pour plus d’informations, consultez la classe <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> dans la documentation de l’API.

## <a name="service-lifetimes"></a>Durées de service

Choisissez une durée de vie appropriée pour chaque service inscrit. Vous pouvez configurer les services ASP.NET Core avec les durées de vie suivantes :

### <a name="transient"></a>Temporaire

Des services à durée de vie temporaire (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) sont créés chaque fois qu’ils sont demandés à partir du conteneur de service. Cette durée de vie convient parfaitement aux services légers et sans état.

Dans les applications qui traitent les demandes, les services transitoires sont supprimés à la fin de la demande.

### <a name="scoped"></a>Délimité

Les services à durée de vie délimitée (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) sont créés une seule fois par requête de client (connexion).

Dans les applications qui traitent les requêtes, les services délimités sont supprimés à la fin de la demande.

> [!WARNING]
> Si vous utilisez un service Scoped dans un middleware, injectez le service dans la méthode `Invoke` ou `InvokeAsync`. N’injectez pas via l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) , car cela force le service à se comporter comme un singleton. Pour plus d'informations, consultez <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Les services avec une durée de vie singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) sont créés la première fois qu’ils sont demandés (ou quand `Startup.ConfigureServices` est exécuté et qu’une instance est spécifiée avec l’inscription du service). Chaque requête ultérieure utilise la même instance. Si l’application exige un comportement singleton, il est recommandé d’autoriser le conteneur de service à gérer la durée de vie du service. N’implémentez pas le modèle de conception singleton et fournissez le code utilisateur pour gérer la durée de vie de l’objet dans la classe.

Dans les applications qui traitent les requêtes, les services Singleton sont supprimés lorsque le <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> est supprimé au moment de l’arrêt de l’application.

> [!WARNING]
> Il est dangereux de résoudre un service délimité depuis un singleton. L’état du service risque de ne pas être correct lors du traitement des requêtes suivantes.

## <a name="service-registration-methods"></a>Méthodes d’inscription du service

Les méthodes d’extension d’inscription de service offrent des surcharges qui sont utiles dans des scénarios spécifiques.

| Méthode | Automatique<br>object<br>suppression | Multiple<br>implémentations | Passage d’args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Exemple :<br>`services.AddSingleton<IMyDep, MyDep>();` | Oui | Oui | Non |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Exemples :<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Oui | Oui | Oui |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Exemple :<br>`services.AddSingleton<MyDep>();` | Oui | Non | Non |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Exemples :<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Non | Oui | Oui |
| `AddSingleton(new {IMPLEMENTATION})`<br>Exemples :<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Non | Non | Oui |

Pour plus d’informations sur la suppression de type, consultez la section [Suppression des services](#disposal-of-services). Un scénario courant d’implémentations multiples est la [simulation de types à des fins de test](xref:test/integration-tests#inject-mock-services).

Les méthodes `TryAdd{LIFETIME}` inscrivent uniquement le service si aucune implémentation n’est inscrite.

Dans l’exemple suivant, la première ligne inscrit `MyDependency` pour `IMyDependency`. La deuxième ligne n’a aucun effet car `IMyDependency` a déjà une implémentation inscrite :

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Pour plus d'informations, consultez les pages suivantes :

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Les méthodes [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) inscrivent uniquement le service en l’absence d’une implémentation *du même type*. Plusieurs services sont résolus par le biais de `IEnumerable<{SERVICE}>`. Lors de l’inscription de services, le développeur ne souhaite ajouter une instance que si une instance du même type n’a pas déjà été ajoutée. En général, cette méthode est utilisée par les créateurs de bibliothèque pour éviter d’inscrire deux copies d’une instance dans le conteneur.

Dans l’exemple suivant, la première ligne inscrit `MyDep` pour `IMyDep1`. La deuxième ligne inscrit `MyDep` pour `IMyDep2`. La troisième ligne n’a aucun effet car `IMyDep1` a déjà une implémentation inscrite de `MyDep` :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Comportement d’injection de constructeurs

Les services peuvent être résolus par deux mécanismes :

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Autorise la création d’objets sans inscription du service dans le conteneur d’injection de dépendances. `ActivatorUtilities` est utilisé avec les abstractions orientées utilisateur, telles que les Tag Helpers, les contrôleurs MVC et les classeurs de modèles.

Les constructeurs peuvent accepter des arguments qui ne sont pas fournis par l’injection de dépendances, mais les arguments doivent affecter des valeurs par défaut.

Lorsque les services sont résolus par `IServiceProvider` ou `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert un constructeur *public* .

Lorsque les services sont résolus par `ActivatorUtilities` , l' [injection de constructeur](xref:mvc/controllers/dependency-injection#constructor-injection) requiert l’existence d’un seul constructeur applicable. Les surcharges de constructeurs sont prises en charge, mais une seule peut exister dont les arguments peuvent tous être satisfaits par l’injection de dépendances.

## <a name="entity-framework-contexts"></a>Contextes Entity Framework

Les contextes Entity Framework sont généralement ajoutés au conteneur de service en utilisant la [durée de vie limitée](#service-lifetimes), car la portée des opérations de base de données d’application web est normalement limitée à la requête du client. La durée de vie par défaut est définie si une durée de vie n’est pas spécifiée par une surcharge [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) lors de l’enregistrement du contexte de base de données. Un service d’une durée de vie donnée ne doit pas utiliser un contexte de base de données dont la durée de vie est plus courte que celle du service.

## <a name="lifetime-and-registration-options"></a>Options de durée de vie et d’inscription

Pour illustrer la différence entre les options de durée de vie et d’inscription, considérez les interfaces suivantes qui représentent des tâches en tant qu’opération avec un identificateur unique, `OperationId`. Selon la façon dont la durée de vie d’un service d’opérations est configurée pour les interfaces suivantes, le conteneur fournit la même instance ou une instance différente du service lorsqu’une classe le demande :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Les interfaces sont implémentées dans la classe `Operation`. Le constructeur `Operation` génère un GUID s’il n’est pas fourni :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Un `OperationService` est inscrit, dépendant de chacun des autres types `Operation`. Lorsque `OperationService` est demandé via l’injection de dépendances, il reçoit une nouvelle instance de chaque service ou une instance existante en fonction de la durée de vie du service dépendant.

* Quand des services temporaires sont créés à la demande à partir du conteneur, le `OperationId` du service `IOperationTransient` est différent du `OperationId` de `OperationService`. `OperationService` reçoit une nouvelle instance de la classe `IOperationTransient`. La nouvelle instance génère un autre `OperationId`.
* Quand des services délimités sont créés pour chaque requête de client, le `OperationId` du `IOperationScoped` service est identique à celui de `OperationService` au sein d’une requête de client. Entre les requêtes de client, les deux services partagent une valeur `OperationId` différente.
* Quand des services singleton et d’instances singleton sont créés une fois et utilisés sur toutes les requêtes de client et tous les services, le `OperationId` est constant entre toutes les requêtes de service.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

Dans `Startup.ConfigureServices`, chaque type est ajouté au conteneur en fonction de sa durée de vie nommée :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Le service `IOperationSingletonInstance` utilise une instance spécifique avec un ID connu `Guid.Empty`. L’utilisation de ce type est facilement identifiable (son GUID n’affiche que des zéros).

L’exemple d’application montre les durées de vie des objets au sein et entre des requêtes individuelles. L’exemple d’application `IndexModel` demande chaque type `IOperation` et `OperationService`. La page affiche ensuite l’ensemble de la classe du modèle de page et des valeurs `OperationId` du service via des assignations de propriété :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Les deux sorties suivantes montrent les résultats de deux requêtes :

**Première requête :**

Opérations du contrôleur :

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Opérations `OperationService` :

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Deuxième requête :**

Opérations du contrôleur :

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Opérations `OperationService` :

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Observez les valeurs `OperationId` qui varient au sein d’une requête et entre les requêtes :

* Les objets *Transient* sont toujours différents. Les valeurs `OperationId` transitoires pour la première et la deuxième requêtes de client sont différentes pour les deux opérations `OperationService` et entre les requêtes de client. Une nouvelle instance est fournie à chaque requête de service et requête de client.
* Les objets *Scoped* sont les mêmes au sein d’une requête de client, mais ils diffèrent entre les requêtes de client.
* Les objets *Singleton* sont les mêmes pour chaque objet et chaque demande, qu’une `Operation` instance soit fournie ou non dans `Startup.ConfigureServices` .

## <a name="call-services-from-main"></a>Appeler des services à partir de Main

Créez un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> avec [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pour résoudre un service délimité dans l’étendue de l’application. Cette approche est pratique pour accéder à un service Scoped au démarrage pour exécuter des tâches d’initialisation. L’exemple suivant montre comment obtenir un contexte pour `MyScopedService` dans `Program.Main` :

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

## <a name="scope-validation"></a>Validation de l’étendue

Quand l’application s’exécute dans l’environnement de développement, le fournisseur de services par défaut effectue des contrôles pour vérifier que :

* Les services Scoped ne sont pas résolus directement ou indirectement à partir du fournisseur de services racine.
* Les services Scoped ne sont pas directement ou indirectement injectés dans des singletons.

Le fournisseur de services racine est créé quand <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> est appelé. La durée de vie du fournisseur de services racine correspond à la durée de vie de l’application/du serveur quand le fournisseur démarre avec l’application et qu’il est supprimé quand l’application s’arrête.

Les services Scoped sont supprimés par le conteneur qui les a créés. Si un service Scoped est créé dans le conteneur racine, la durée de vie du service est promue en singleton, car elle est supprimée par le conteneur racine seulement quand l’application/le serveur est arrêté. La validation des étendues du service permet de traiter ces situations quand `BuildServiceProvider` est appelé.

Pour plus d'informations, consultez <xref:fundamentals/host/web-host#scope-validation>.   

## <a name="request-services"></a>Services de requête

Les services disponibles au sein d’une requête ASP.NET Core à partir de `HttpContext` sont exposés par le biais de la collection [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Les services de requête représentent les services configurés et demandés dans le cadre de l’application. Lorsque les objets spécifient des dépendances, ceux-ci sont satisfaits par les types trouvés dans `RequestServices`, pas dans `ApplicationServices`.

En règle générale, l’application ne doit pas utiliser directement ces propriétés. Demandez plutôt les types nécessaires à la classe via des constructeurs de classe et autorisez le framework à injecter les dépendances. Cela génère des classes qui sont plus faciles à tester.

> [!NOTE]
> Préférez demander des dépendances en tant que paramètres de constructeur plutôt qu’accéder à la collection `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Conception de services pour l’injection de dépendances

Les bonnes pratiques permettent de :

* Concevoir des services afin d’utiliser l’injection de dépendances pour obtenir leurs dépendances.
* Évitez les classes et les membres avec état, static. Concevez des applications pour utiliser des services Singleton à la place, ce qui évite de créer un état global.
* Éviter une instanciation directe de classes dépendantes au sein de services. L’instanciation directe associe le code à une implémentation particulière.
* Limiter la taille des classes d’application, faire en sorte qu’elles soient bien factorisées et facilement testées.

Si une classe semble avoir trop de dépendances injectées, cela signifie généralement que la classe a trop de responsabilités et viole le [principe de responsabilité unique](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Essayez de refactoriser la classe en déplaçant certaines de ses responsabilités dans une nouvelle classe. Gardez à l’esprit que Razor les classes de modèle de page de pages et les classes de contrôleur MVC doivent se concentrer sur les préoccupations de l’interface utilisateur. Les règles d’entreprise et les détails d’implémentation de l’accès aux données doivent être conservés dans les classes appropriées à ces [préoccupations distinctes](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Suppression des services

Le conteneur appelle <xref:System.IDisposable.Dispose*> pour les types <xref:System.IDisposable> qu’il crée. Si une instance est ajoutée au conteneur par le code utilisateur, elle n’est pas supprimée automatiquement.

Dans l’exemple suivant, les services sont créés par le conteneur de service et supprimés automatiquement :

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

Dans l’exemple suivant :

* Les instances de service ne sont pas créées par le conteneur de service.
* Les durées de vie de service prévues ne sont pas connues de l’infrastructure.
* L’infrastructure ne supprime pas automatiquement les services.
* Si les services ne sont pas explicitement supprimés dans le code du développeur, ils persistent jusqu’à ce que l’application s’arrête.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Recommandations IDisposable pour les instances temporaires et partagées

#### <a name="transient-limited-lifetime"></a>Transitoire, durée de vie limitée

**Scénario**

L’application requiert une <xref:System.IDisposable> instance avec une durée de vie transitoire pour l’un des scénarios suivants :

* L’instance est résolue dans l’étendue racine.
* L’instance doit être supprimée avant la fin de l’étendue.

**Solution**

Utilisez le modèle de fabrique pour créer une instance en dehors de l’étendue parente. Dans ce cas, l’application a généralement une `Create` méthode qui appelle directement le constructeur du type final. Si le type final a d’autres dépendances, la fabrique peut :

* Recevoir un <xref:System.IServiceProvider> dans son constructeur.
* Utilisez <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> pour instancier l’instance en dehors du conteneur, tout en utilisant le conteneur pour ses dépendances.

#### <a name="shared-instance-limited-lifetime"></a>Instance partagée, durée de vie limitée

**Scénario**

L’application nécessite une <xref:System.IDisposable> instance partagée sur plusieurs services, mais le <xref:System.IDisposable> doit avoir une durée de vie limitée.

**Solution**

Inscrivez l’instance avec une durée de vie limitée. Utilisez <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> pour démarrer et créer un nouveau <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . Utilisez les étendues <xref:System.IServiceProvider> pour accéder aux services requis. Supprimez l’étendue lorsque la durée de vie doit se terminer.

#### <a name="general-guidelines"></a>Instructions générales

* N’inscrivez pas <xref:System.IDisposable> les instances avec une étendue transitoire. Utilisez à la place le modèle de fabrique.
* Ne résolvez pas les instances temporaires ou délimitées <xref:System.IDisposable> dans l’étendue racine. La seule exception générale est lorsque l’application crée/recrée et supprime le <xref:System.IServiceProvider> , qui n’est pas un modèle idéal.
* La réception d’une <xref:System.IDisposable> dépendance via l’injection de dépendances ne nécessite pas que le récepteur s’implémente <xref:System.IDisposable> lui-même. Le récepteur de la <xref:System.IDisposable> dépendance ne doit pas appeler <xref:System.IDisposable.Dispose%2A> sur cette dépendance.
* Les portées doivent être utilisées pour contrôler les durées de vie des services. Les étendues ne sont pas hiérarchiques, et il n’existe pas de connexion spéciale entre les étendues.

## <a name="default-service-container-replacement"></a>Remplacement de conteneur de services par défaut

Le conteneur de service intégré est conçu pour répondre aux besoins de l’infrastructure et de la plupart des applications grand public. Nous vous recommandons d’utiliser le conteneur intégré, sauf si vous avez besoin d’une fonctionnalité spécifique que le conteneur intégré ne prend pas en charge, par exemple :

* Injection de propriétés
* Injection en fonction du nom
* Conteneurs enfants
* Gestion personnalisée de la durée de vie
* `Func<T>` prend en charge l’initialisation tardive
* Inscription basée sur une convention

Les conteneurs tiers suivants peuvent être utilisés avec les applications ASP.NET Core :

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Sécurité des threads

Créez des services singleton thread-safe. Si un service singleton a une dépendance vis-à-vis d’un service Transient, ce dernier peut également nécessiter la cohérence de thread, en fonction de la manière dont il est utilisé par le singleton.

La méthode de fabrique d’un service unique, telle que le deuxième argument de [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), n’a pas besoin d’être thread-safe. Comme un constructeur de type (`static`), elle est forcément appelée une fois par un seul thread.

## <a name="recommendations"></a>Recommandations

* La résolution de service basée sur `async/await` et `Task` n’est pas prise en charge. C# ne prend pas en charge les constructeurs asynchrones ; par conséquent, le modèle recommandé consiste à utiliser des méthodes asynchrones après avoir résolu le service de façon synchrone.

* Évitez de stocker des données et des configurations directement dans le conteneur de services. Par exemple, le panier d’achat d’un utilisateur ne doit en général pas être ajouté au conteneur de services. La configuration doit utiliser le [modèle d’options](xref:fundamentals/configuration/options). De même, évitez les objets « conteneurs de données » qui n’existent que pour autoriser l’accès à un autre objet. Il est préférable de demander l’élément réel par le biais de l’injection de dépendance.
* Évitez l’accès statique aux services. Par exemple, évitez de taper statiquement [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pour une utilisation ailleurs.

* Évitez d’utiliser le *modèle de localisation de service*, qui combine les stratégies [d’inversion de contrôle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .
  * N’appelez pas <xref:System.IServiceProvider.GetService*> pour obtenir une instance de service lorsque vous pouvez utiliser l’injection de code à la place :

    **Incorrect :**

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
   
    **Correct**:

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

* Évitez d’injecter une fabrique qui résout les dépendances au moment de l’exécution à l’aide de <xref:System.IServiceProvider.GetService*> .
* Évitez l’accès statique à `HttpContext` (par exemple, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Comme pour toutes les recommandations, vous pouvez vous trouver dans des situations où il est nécessaire d’ignorer une recommandation. Les exceptions sont rares, principalement des cas spéciaux dans l’infrastructure elle-même.

L’injection de dépendance constitue une *alternative* aux modèles d’accès aux objets statiques/globaux. Il est possible que vous ne bénéficiez pas des avantages de l’injection de dépendances si vous la combinez avec l’accès aux objets statiques.

## <a name="additional-resources"></a>Ressources complémentaires

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Quatre méthodes pour supprimer des IDisposable dans ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Écrire un code clair dans ASP.NET Core avec l’injection de dépendance (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Principe des dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Conteneurs d’inversion de contrôle et modèle d’injection de dépendances (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Guide pratique pour inscrire un service comportant plusieurs interfaces dans l’injection de dépendance ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
