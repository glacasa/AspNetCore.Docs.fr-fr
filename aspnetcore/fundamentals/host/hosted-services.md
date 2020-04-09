---
title: Tâches d’arrière-plan avec des services hébergés dans ASP.NET Core
author: rick-anderson
description: Découvrez comment implémenter des tâches d’arrière-plan avec des services hébergés dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d3f409170eedd281fd7608c4b9835bf9443c49b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666200"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Tâches d’arrière-plan avec des services hébergés dans ASP.NET Core

Par [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

Dans ASP.NET Core, les tâches d’arrière-plan peuvent être implémentées en tant que *services hébergés*. Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Cette rubrique contient trois exemples de service hébergé :

* Tâche d’arrière-plan qui s’exécute sur un minuteur.
* Service hébergé qui active un [service à portée](xref:fundamentals/dependency-injection#service-lifetimes). Le service à portée peut utiliser [l’injection de dépendance (DI)](xref:fundamentals/dependency-injection).
* Tâches d’arrière-plan en file d’attente qui s’exécutent séquentiellement.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Modèle Service Worker

Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables. Une application créée à partir du modèle de service aux travailleurs spécifie le Travailleur SDK dans son dossier de projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Pour utiliser le modèle en tant que base d’une application de services hébergés :

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Package

Une application basée sur le `Microsoft.NET.Sdk.Worker` modèle de service de travailleur utilise le SDK et dispose d’une référence explicite de paquet au paquet [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) Par exemple, consultez le fichier de projet de l’application d’échantillon *(BackgroundTasksSample.csproj*).

Pour les applications `Microsoft.NET.Sdk.Web` Web qui utilisent le SDK, le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) est référencé implicitement à partir du cadre partagé. Une référence de paquet explicite dans le fichier de projet de l’application n’est pas requise.

## <a name="ihostedservice-interface"></a>Interface IHostedService

L’interface <xref:Microsoft.Extensions.Hosting.IHostedService> définit deux méthodes pour les objets qui sont gérés par l’hôte :

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contient la logique pour démarrer la tâche d’arrière-plan. `StartAsync`est appelé *avant:*

  * Le pipeline de traitement des demandes`Startup.Configure`de l’application est configuré ().
  * Le serveur est démarré et [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) est déclenché.

  Le comportement par défaut peut être modifié `StartAsync` de sorte que le service hébergé `ApplicationStarted` fonctionne après que le pipeline de l’application a été configuré et est appelé. Pour modifier le comportement par défaut,`VideosWatcher` ajoutez le service `ConfigureWebHostDefaults`hébergé (dans l’exemple suivant) après avoir appelé :

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash;, déclenchée quand l’hôte effectue un arrêt normal. `StopAsync` contient la logique pour terminer la tâche d’arrière-plan. Implémentez <xref:System.IDisposable> et les [finaliseurs (destructeurs)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pour supprimer toutes les ressources non managées.

  Le jeton d’annulation a un délai d’expiration par défaut de cinq secondes pour indiquer que le processus d’arrêt ne doit plus être normal. Quand l’annulation est demandée sur le jeton :

  * Les opérations en arrière-plan restantes effectuées par l’application doivent être abandonnées.
  * Les méthodes appelées dans `StopAsync` doivent retourner rapidement.

  Cependant, les tâches ne sont pas abandonnées après la demande d’annulation : l’appelant attend que toutes les tâches se terminent.

  Si l’application s’arrête inopinément (par exemple en cas d’échec du processus de l’application), `StopAsync` n’est probablement pas appelée. Par conséquent, les méthodes appelées ou les opérations effectuées dans `StopAsync` peuvent ne pas se produire.

  Pour prolonger le délai d’expiration par défaut de cinq secondes, définissez :

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quand vous utilisez l’hôte générique. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Le paramètre de configuration du délai d’expiration de l’hôte quand vous utilisez l’hôte web. Pour plus d’informations, consultez <xref:fundamentals/host/web-host#shutdown-timeout>.

Le service hébergé est activé une seule fois au démarrage de l’application et s’arrête normalement à l’arrêt de l’application. Si une erreur est levée pendant l’exécution des tâches d’arrière-plan, `Dispose` doit être appelée même si `StopAsync` n’est pas appelée.

## <a name="backgroundservice-base-class"></a>Classe de base BackgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService>est une classe de base <xref:Microsoft.Extensions.Hosting.IHostedService>pour la mise en œuvre d’une longue durée .

[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) est appelé pour exécuter le service d’arrière-plan. La mise <xref:System.Threading.Tasks.Task> en œuvre renvoie un qui représente toute la durée de vie du service d’arrière-plan. Aucun autre service n’est lancé jusqu’à ce qu’ExecuteAsync [devienne asynchrone,](https://github.com/dotnet/extensions/issues/2149)par exemple en appelant `await`. Évitez d’effectuer de longues `ExecuteAsync`performances, bloquant le travail d’initialisation dans . Les blocs d’hôtes de [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) attendent `ExecuteAsync` de se terminer.

Le jeton d’annulation est déclenché lorsque [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) est appelé. Votre mise `ExecuteAsync` en œuvre de devrait se terminer rapidement lorsque le jeton d’annulation est tiré afin de fermer gracieusement le service. Dans le cas contraire, le service s’arrête sans frais lors du délai d’arrêt. Pour plus d’informations, consultez la section [interface IHostedService.](#ihostedservice-interface)

## <a name="timed-background-tasks"></a>Tâche d’arrière-plan avec minuteur

Une tâche d’arrière-plan avec minuteur utilise la classe [System.Threading.Timer](xref:System.Threading.Timer). Le minuteur déclenche la méthode `DoWork` de la tâche. Le minuteur est désactivé sur `StopAsync` et supprimé quand le conteneur du service est supprimé sur `Dispose` :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

Le <xref:System.Threading.Timer> n’attend pas pour `DoWork` les exécutions précédentes de se terminer, de sorte que l’approche montrée pourrait ne pas convenir à tous les scénarios. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) est utilisé pour incrémenter le compteur d’exécution comme une opération `executionCount` atomique, ce qui garantit que plusieurs threads ne se mettent pas à jour simultanément.

Le service est `IHostBuilder.ConfigureServices` enregistré dans (*Program.cs*) avec la méthode d’extension: `AddHostedService`

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Utilisation d’un service délimité dans une tâche d’arrière-plan

Pour utiliser les [services à portée](xref:fundamentals/dependency-injection#service-lifetimes) dans un service [d’arrière-plan,](#backgroundservice-base-class)créez une portée. Par défaut, aucune étendue n’est créée pour un service hébergé.

Le service des tâches d’arrière-plan délimitées contient la logique de la tâche d’arrière-plan. Dans l’exemple suivant :

* Le service est asynchrone. La méthode `DoWork` retourne un `Task`. Pour des raisons de démonstration, un `DoWork` délai de dix secondes est attendu dans la méthode.
* Un <xref:Microsoft.Extensions.Logging.ILogger> est injecté dans le service.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Le service hébergé crée une portée pour résoudre le `DoWork` service de tâches d’arrière-plan à portée d’action pour appeler sa méthode. `DoWork`retourne `Task`un , qui `ExecuteAsync`est attendu dans :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Les services sont `IHostBuilder.ConfigureServices` enregistrés dans (*Program.cs*). Le service hébergé est `AddHostedService` enregistré avec la méthode d’extension :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tâches d’arrière-plan en file d’attente

Une file d’attente de tâches de <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> fond est basée sur le .NET 4.x[(provisoirement prévu pour être intégré pour ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

Dans l’exemple suivant `QueueHostedService` :

* La `BackgroundProcessing` méthode `Task`renvoie un `ExecuteAsync`, qui est attendu dans .
* Les tâches d’arrière-plan dans la `BackgroundProcessing`file d’attente sont déqueued et exécutées dans .
* Les éléments de travail sont `StopAsync`attendus avant l’arrêt du service.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Un `MonitorLoop` service gère les tâches d’enqueuing `w` pour le service hébergé chaque fois que la clé est sélectionnée sur un périphérique d’entrée :

* Le `IBackgroundTaskQueue` est injecté `MonitorLoop` dans le service.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`est appelé à enqueue un élément de travail.
* L’élément de travail simule une tâche d’arrière-plan de longue durée :
  * Trois retards de 5`Task.Delay`secondes sont exécutés ( ).
  * Une `try-catch` déclaration <xref:System.OperationCanceledException> piège si la tâche est annulée.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Les services sont `IHostBuilder.ConfigureServices` enregistrés dans (*Program.cs*). Le service hébergé est `AddHostedService` enregistré avec la méthode d’extension :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MontiorLoop`est commencé `Program.Main`dans :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dans ASP.NET Core, les tâches d’arrière-plan peuvent être implémentées en tant que *services hébergés*. Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Cette rubrique contient trois exemples de service hébergé :

* Tâche d’arrière-plan qui s’exécute sur un minuteur.
* Service hébergé qui active un [service à portée](xref:fundamentals/dependency-injection#service-lifetimes). Le service à portée peut utiliser [l’injection de dépendance (DI)](xref:fundamentals/dependency-injection)
* Tâches d’arrière-plan en file d’attente qui s’exécutent séquentiellement.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="package"></a>Package

Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interface IHostedService

Les services hébergés implémentent l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>. L’interface définit deux méthodes pour les objets qui sont gérés par l’hôte :

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contient la logique pour démarrer la tâche d’arrière-plan. Lors de l’utilisation de [l’hébergeur Web](xref:fundamentals/host/web-host), `StartAsync` est appelé après le serveur a commencé et [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) est déclenché. Lors de l’utilisation de `ApplicationStarted` [l’hôte générique](xref:fundamentals/host/generic-host), `StartAsync` est appelé avant est déclenché.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash;, déclenchée quand l’hôte effectue un arrêt normal. `StopAsync` contient la logique pour terminer la tâche d’arrière-plan. Implémentez <xref:System.IDisposable> et les [finaliseurs (destructeurs)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pour supprimer toutes les ressources non managées.

  Le jeton d’annulation a un délai d’expiration par défaut de cinq secondes pour indiquer que le processus d’arrêt ne doit plus être normal. Quand l’annulation est demandée sur le jeton :

  * Les opérations en arrière-plan restantes effectuées par l’application doivent être abandonnées.
  * Les méthodes appelées dans `StopAsync` doivent retourner rapidement.

  Cependant, les tâches ne sont pas abandonnées après la demande d’annulation : l’appelant attend que toutes les tâches se terminent.

  Si l’application s’arrête inopinément (par exemple en cas d’échec du processus de l’application), `StopAsync` n’est probablement pas appelée. Par conséquent, les méthodes appelées ou les opérations effectuées dans `StopAsync` peuvent ne pas se produire.

  Pour prolonger le délai d’expiration par défaut de cinq secondes, définissez :

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quand vous utilisez l’hôte générique. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Le paramètre de configuration du délai d’expiration de l’hôte quand vous utilisez l’hôte web. Pour plus d’informations, consultez <xref:fundamentals/host/web-host#shutdown-timeout>.

Le service hébergé est activé une seule fois au démarrage de l’application et s’arrête normalement à l’arrêt de l’application. Si une erreur est levée pendant l’exécution des tâches d’arrière-plan, `Dispose` doit être appelée même si `StopAsync` n’est pas appelée.

## <a name="timed-background-tasks"></a>Tâche d’arrière-plan avec minuteur

Une tâche d’arrière-plan avec minuteur utilise la classe [System.Threading.Timer](xref:System.Threading.Timer). Le minuteur déclenche la méthode `DoWork` de la tâche. Le minuteur est désactivé sur `StopAsync` et supprimé quand le conteneur du service est supprimé sur `Dispose` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Le <xref:System.Threading.Timer> n’attend pas pour `DoWork` les exécutions précédentes de se terminer, de sorte que l’approche montrée pourrait ne pas convenir à tous les scénarios.

Le service est inscrit dans `Startup.ConfigureServices` avec la méthode d’extension `AddHostedService` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Utilisation d’un service délimité dans une tâche d’arrière-plan

Pour utiliser les services `IHostedService`à [portée](xref:fundamentals/dependency-injection#service-lifetimes) dans un, créer une portée. Par défaut, aucune étendue n’est créée pour un service hébergé.

Le service des tâches d’arrière-plan délimitées contient la logique de la tâche d’arrière-plan. Dans l’exemple suivant, un <xref:Microsoft.Extensions.Logging.ILogger> est injecté dans le service :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Le service hébergé crée une étendue pour résoudre le service des tâches d’arrière-plan délimitées pour appeler sa méthode `DoWork` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Les services sont inscrits dans `Startup.ConfigureServices`. L’implémentation `IHostedService` est inscrite avec la méthode d’extension `AddHostedService` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tâches d’arrière-plan en file d’attente

Une file d’attente de tâches de fond <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> est basée sur le cadre .NET 4.x[(provisoirement prévu pour être intégré pour ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)) :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

Dans `QueueHostedService`, les tâches d'arrière-plan dans la file d'attente sont retirées de la file d'attente et exécutées en tant que [BackgroundService](#backgroundservice-base-class), qui est une classe de base pour l’implémentation d’une exécution longue `IHostedService` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Les services sont inscrits dans `Startup.ConfigureServices`. L’implémentation `IHostedService` est inscrite avec la méthode d’extension `AddHostedService` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Dans la classe de modèle de page Index :

* `IBackgroundTaskQueue` est injecté dans le constructeur et affecté à `Queue`.
* Un <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> est injecté et affecté à `_serviceScopeFactory`. La fabrique est utilisée pour créer des instances de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, qui servent à créer des services au sein d’une étendue. Une étendue est créée de façon à utiliser l’élément `AppDbContext` ([service délimité](xref:fundamentals/dependency-injection#service-lifetimes)) de l’application pour écrire des enregistrements de base de données dans `IBackgroundTaskQueue` (service singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Quand le bouton **Ajouter une tâche** est sélectionné dans la page Index, la méthode `OnPostAddTask` est exécutée. `QueueBackgroundWorkItem`est appelé à enqueue un élément de travail:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Implémenter des tâches d’arrière-plan dans des microservices avec IHostedService et la classe BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Exécuter des tâches en arrière-plan avec WebJobs dans Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
