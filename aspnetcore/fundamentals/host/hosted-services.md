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
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="01493-103">Tâches d’arrière-plan avec des services hébergés dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01493-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="01493-104">Par [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="01493-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01493-105">Dans ASP.NET Core, les tâches d’arrière-plan peuvent être implémentées en tant que *services hébergés*.</span><span class="sxs-lookup"><span data-stu-id="01493-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="01493-106">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="01493-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="01493-107">Cette rubrique contient trois exemples de service hébergé :</span><span class="sxs-lookup"><span data-stu-id="01493-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="01493-108">Tâche d’arrière-plan qui s’exécute sur un minuteur.</span><span class="sxs-lookup"><span data-stu-id="01493-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="01493-109">Service hébergé qui active un [service à portée](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="01493-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="01493-110">Le service à portée peut utiliser [l’injection de dépendance (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="01493-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="01493-111">Tâches d’arrière-plan en file d’attente qui s’exécutent séquentiellement.</span><span class="sxs-lookup"><span data-stu-id="01493-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="01493-112">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01493-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="01493-113">Modèle Service Worker</span><span class="sxs-lookup"><span data-stu-id="01493-113">Worker Service template</span></span>

<span data-ttu-id="01493-114">Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables.</span><span class="sxs-lookup"><span data-stu-id="01493-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="01493-115">Une application créée à partir du modèle de service aux travailleurs spécifie le Travailleur SDK dans son dossier de projet :</span><span class="sxs-lookup"><span data-stu-id="01493-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="01493-116">Pour utiliser le modèle en tant que base d’une application de services hébergés :</span><span class="sxs-lookup"><span data-stu-id="01493-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="01493-117">Package</span><span class="sxs-lookup"><span data-stu-id="01493-117">Package</span></span>

<span data-ttu-id="01493-118">Une application basée sur le `Microsoft.NET.Sdk.Worker` modèle de service de travailleur utilise le SDK et dispose d’une référence explicite de paquet au paquet [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)</span><span class="sxs-lookup"><span data-stu-id="01493-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="01493-119">Par exemple, consultez le fichier de projet de l’application d’échantillon *(BackgroundTasksSample.csproj*).</span><span class="sxs-lookup"><span data-stu-id="01493-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="01493-120">Pour les applications `Microsoft.NET.Sdk.Web` Web qui utilisent le SDK, le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) est référencé implicitement à partir du cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="01493-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="01493-121">Une référence de paquet explicite dans le fichier de projet de l’application n’est pas requise.</span><span class="sxs-lookup"><span data-stu-id="01493-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="01493-122">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="01493-122">IHostedService interface</span></span>

<span data-ttu-id="01493-123">L’interface <xref:Microsoft.Extensions.Hosting.IHostedService> définit deux méthodes pour les objets qui sont gérés par l’hôte :</span><span class="sxs-lookup"><span data-stu-id="01493-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="01493-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contient la logique pour démarrer la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="01493-125">`StartAsync`est appelé *avant:*</span><span class="sxs-lookup"><span data-stu-id="01493-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="01493-126">Le pipeline de traitement des demandes`Startup.Configure`de l’application est configuré ().</span><span class="sxs-lookup"><span data-stu-id="01493-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="01493-127">Le serveur est démarré et [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="01493-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="01493-128">Le comportement par défaut peut être modifié `StartAsync` de sorte que le service hébergé `ApplicationStarted` fonctionne après que le pipeline de l’application a été configuré et est appelé.</span><span class="sxs-lookup"><span data-stu-id="01493-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="01493-129">Pour modifier le comportement par défaut,`VideosWatcher` ajoutez le service `ConfigureWebHostDefaults`hébergé (dans l’exemple suivant) après avoir appelé :</span><span class="sxs-lookup"><span data-stu-id="01493-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="01493-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash;, déclenchée quand l’hôte effectue un arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="01493-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="01493-131">`StopAsync` contient la logique pour terminer la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="01493-132">Implémentez <xref:System.IDisposable> et les [finaliseurs (destructeurs)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pour supprimer toutes les ressources non managées.</span><span class="sxs-lookup"><span data-stu-id="01493-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="01493-133">Le jeton d’annulation a un délai d’expiration par défaut de cinq secondes pour indiquer que le processus d’arrêt ne doit plus être normal.</span><span class="sxs-lookup"><span data-stu-id="01493-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="01493-134">Quand l’annulation est demandée sur le jeton :</span><span class="sxs-lookup"><span data-stu-id="01493-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="01493-135">Les opérations en arrière-plan restantes effectuées par l’application doivent être abandonnées.</span><span class="sxs-lookup"><span data-stu-id="01493-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="01493-136">Les méthodes appelées dans `StopAsync` doivent retourner rapidement.</span><span class="sxs-lookup"><span data-stu-id="01493-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="01493-137">Cependant, les tâches ne sont pas abandonnées après la demande d’annulation : l’appelant attend que toutes les tâches se terminent.</span><span class="sxs-lookup"><span data-stu-id="01493-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="01493-138">Si l’application s’arrête inopinément (par exemple en cas d’échec du processus de l’application), `StopAsync` n’est probablement pas appelée.</span><span class="sxs-lookup"><span data-stu-id="01493-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="01493-139">Par conséquent, les méthodes appelées ou les opérations effectuées dans `StopAsync` peuvent ne pas se produire.</span><span class="sxs-lookup"><span data-stu-id="01493-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="01493-140">Pour prolonger le délai d’expiration par défaut de cinq secondes, définissez :</span><span class="sxs-lookup"><span data-stu-id="01493-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="01493-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quand vous utilisez l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="01493-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="01493-142">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="01493-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="01493-143">Le paramètre de configuration du délai d’expiration de l’hôte quand vous utilisez l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="01493-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="01493-144">Pour plus d’informations, consultez <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="01493-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="01493-145">Le service hébergé est activé une seule fois au démarrage de l’application et s’arrête normalement à l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="01493-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="01493-146">Si une erreur est levée pendant l’exécution des tâches d’arrière-plan, `Dispose` doit être appelée même si `StopAsync` n’est pas appelée.</span><span class="sxs-lookup"><span data-stu-id="01493-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="01493-147">Classe de base BackgroundService</span><span class="sxs-lookup"><span data-stu-id="01493-147">BackgroundService base class</span></span>

<span data-ttu-id="01493-148"><xref:Microsoft.Extensions.Hosting.BackgroundService>est une classe de base <xref:Microsoft.Extensions.Hosting.IHostedService>pour la mise en œuvre d’une longue durée .</span><span class="sxs-lookup"><span data-stu-id="01493-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="01493-149">[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) est appelé pour exécuter le service d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="01493-150">La mise <xref:System.Threading.Tasks.Task> en œuvre renvoie un qui représente toute la durée de vie du service d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="01493-151">Aucun autre service n’est lancé jusqu’à ce qu’ExecuteAsync [devienne asynchrone,](https://github.com/dotnet/extensions/issues/2149)par exemple en appelant `await`.</span><span class="sxs-lookup"><span data-stu-id="01493-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="01493-152">Évitez d’effectuer de longues `ExecuteAsync`performances, bloquant le travail d’initialisation dans .</span><span class="sxs-lookup"><span data-stu-id="01493-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="01493-153">Les blocs d’hôtes de [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) attendent `ExecuteAsync` de se terminer.</span><span class="sxs-lookup"><span data-stu-id="01493-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="01493-154">Le jeton d’annulation est déclenché lorsque [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) est appelé.</span><span class="sxs-lookup"><span data-stu-id="01493-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="01493-155">Votre mise `ExecuteAsync` en œuvre de devrait se terminer rapidement lorsque le jeton d’annulation est tiré afin de fermer gracieusement le service.</span><span class="sxs-lookup"><span data-stu-id="01493-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="01493-156">Dans le cas contraire, le service s’arrête sans frais lors du délai d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="01493-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="01493-157">Pour plus d’informations, consultez la section [interface IHostedService.](#ihostedservice-interface)</span><span class="sxs-lookup"><span data-stu-id="01493-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="01493-158">Tâche d’arrière-plan avec minuteur</span><span class="sxs-lookup"><span data-stu-id="01493-158">Timed background tasks</span></span>

<span data-ttu-id="01493-159">Une tâche d’arrière-plan avec minuteur utilise la classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="01493-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="01493-160">Le minuteur déclenche la méthode `DoWork` de la tâche.</span><span class="sxs-lookup"><span data-stu-id="01493-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="01493-161">Le minuteur est désactivé sur `StopAsync` et supprimé quand le conteneur du service est supprimé sur `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="01493-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="01493-162">Le <xref:System.Threading.Timer> n’attend pas pour `DoWork` les exécutions précédentes de se terminer, de sorte que l’approche montrée pourrait ne pas convenir à tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="01493-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="01493-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) est utilisé pour incrémenter le compteur d’exécution comme une opération `executionCount` atomique, ce qui garantit que plusieurs threads ne se mettent pas à jour simultanément.</span><span class="sxs-lookup"><span data-stu-id="01493-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="01493-164">Le service est `IHostBuilder.ConfigureServices` enregistré dans (*Program.cs*) avec la méthode d’extension: `AddHostedService`</span><span class="sxs-lookup"><span data-stu-id="01493-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="01493-165">Utilisation d’un service délimité dans une tâche d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="01493-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="01493-166">Pour utiliser les [services à portée](xref:fundamentals/dependency-injection#service-lifetimes) dans un service [d’arrière-plan,](#backgroundservice-base-class)créez une portée.</span><span class="sxs-lookup"><span data-stu-id="01493-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="01493-167">Par défaut, aucune étendue n’est créée pour un service hébergé.</span><span class="sxs-lookup"><span data-stu-id="01493-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="01493-168">Le service des tâches d’arrière-plan délimitées contient la logique de la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="01493-169">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="01493-169">In the following example:</span></span>

* <span data-ttu-id="01493-170">Le service est asynchrone.</span><span class="sxs-lookup"><span data-stu-id="01493-170">The service is asynchronous.</span></span> <span data-ttu-id="01493-171">La méthode `DoWork` retourne un `Task`.</span><span class="sxs-lookup"><span data-stu-id="01493-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="01493-172">Pour des raisons de démonstration, un `DoWork` délai de dix secondes est attendu dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="01493-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="01493-173">Un <xref:Microsoft.Extensions.Logging.ILogger> est injecté dans le service.</span><span class="sxs-lookup"><span data-stu-id="01493-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="01493-174">Le service hébergé crée une portée pour résoudre le `DoWork` service de tâches d’arrière-plan à portée d’action pour appeler sa méthode.</span><span class="sxs-lookup"><span data-stu-id="01493-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="01493-175">`DoWork`retourne `Task`un , qui `ExecuteAsync`est attendu dans :</span><span class="sxs-lookup"><span data-stu-id="01493-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="01493-176">Les services sont `IHostBuilder.ConfigureServices` enregistrés dans (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="01493-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="01493-177">Le service hébergé est `AddHostedService` enregistré avec la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="01493-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="01493-178">Tâches d’arrière-plan en file d’attente</span><span class="sxs-lookup"><span data-stu-id="01493-178">Queued background tasks</span></span>

<span data-ttu-id="01493-179">Une file d’attente de tâches de <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> fond est basée sur le .NET 4.x[(provisoirement prévu pour être intégré pour ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="01493-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="01493-180">Dans l’exemple suivant `QueueHostedService` :</span><span class="sxs-lookup"><span data-stu-id="01493-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="01493-181">La `BackgroundProcessing` méthode `Task`renvoie un `ExecuteAsync`, qui est attendu dans .</span><span class="sxs-lookup"><span data-stu-id="01493-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="01493-182">Les tâches d’arrière-plan dans la `BackgroundProcessing`file d’attente sont déqueued et exécutées dans .</span><span class="sxs-lookup"><span data-stu-id="01493-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="01493-183">Les éléments de travail sont `StopAsync`attendus avant l’arrêt du service.</span><span class="sxs-lookup"><span data-stu-id="01493-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="01493-184">Un `MonitorLoop` service gère les tâches d’enqueuing `w` pour le service hébergé chaque fois que la clé est sélectionnée sur un périphérique d’entrée :</span><span class="sxs-lookup"><span data-stu-id="01493-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="01493-185">Le `IBackgroundTaskQueue` est injecté `MonitorLoop` dans le service.</span><span class="sxs-lookup"><span data-stu-id="01493-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="01493-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem`est appelé à enqueue un élément de travail.</span><span class="sxs-lookup"><span data-stu-id="01493-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="01493-187">L’élément de travail simule une tâche d’arrière-plan de longue durée :</span><span class="sxs-lookup"><span data-stu-id="01493-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="01493-188">Trois retards de 5`Task.Delay`secondes sont exécutés ( ).</span><span class="sxs-lookup"><span data-stu-id="01493-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="01493-189">Une `try-catch` déclaration <xref:System.OperationCanceledException> piège si la tâche est annulée.</span><span class="sxs-lookup"><span data-stu-id="01493-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="01493-190">Les services sont `IHostBuilder.ConfigureServices` enregistrés dans (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="01493-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="01493-191">Le service hébergé est `AddHostedService` enregistré avec la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="01493-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="01493-192">`MontiorLoop`est commencé `Program.Main`dans :</span><span class="sxs-lookup"><span data-stu-id="01493-192">`MontiorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="01493-193">Dans ASP.NET Core, les tâches d’arrière-plan peuvent être implémentées en tant que *services hébergés*.</span><span class="sxs-lookup"><span data-stu-id="01493-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="01493-194">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="01493-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="01493-195">Cette rubrique contient trois exemples de service hébergé :</span><span class="sxs-lookup"><span data-stu-id="01493-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="01493-196">Tâche d’arrière-plan qui s’exécute sur un minuteur.</span><span class="sxs-lookup"><span data-stu-id="01493-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="01493-197">Service hébergé qui active un [service à portée](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="01493-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="01493-198">Le service à portée peut utiliser [l’injection de dépendance (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="01493-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="01493-199">Tâches d’arrière-plan en file d’attente qui s’exécutent séquentiellement.</span><span class="sxs-lookup"><span data-stu-id="01493-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="01493-200">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01493-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="01493-201">Package</span><span class="sxs-lookup"><span data-stu-id="01493-201">Package</span></span>

<span data-ttu-id="01493-202">Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="01493-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="01493-203">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="01493-203">IHostedService interface</span></span>

<span data-ttu-id="01493-204">Les services hébergés implémentent l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="01493-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="01493-205">L’interface définit deux méthodes pour les objets qui sont gérés par l’hôte :</span><span class="sxs-lookup"><span data-stu-id="01493-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="01493-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contient la logique pour démarrer la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="01493-207">Lors de l’utilisation de [l’hébergeur Web](xref:fundamentals/host/web-host), `StartAsync` est appelé après le serveur a commencé et [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="01493-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="01493-208">Lors de l’utilisation de `ApplicationStarted` [l’hôte générique](xref:fundamentals/host/generic-host), `StartAsync` est appelé avant est déclenché.</span><span class="sxs-lookup"><span data-stu-id="01493-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="01493-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash;, déclenchée quand l’hôte effectue un arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="01493-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="01493-210">`StopAsync` contient la logique pour terminer la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="01493-211">Implémentez <xref:System.IDisposable> et les [finaliseurs (destructeurs)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pour supprimer toutes les ressources non managées.</span><span class="sxs-lookup"><span data-stu-id="01493-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="01493-212">Le jeton d’annulation a un délai d’expiration par défaut de cinq secondes pour indiquer que le processus d’arrêt ne doit plus être normal.</span><span class="sxs-lookup"><span data-stu-id="01493-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="01493-213">Quand l’annulation est demandée sur le jeton :</span><span class="sxs-lookup"><span data-stu-id="01493-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="01493-214">Les opérations en arrière-plan restantes effectuées par l’application doivent être abandonnées.</span><span class="sxs-lookup"><span data-stu-id="01493-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="01493-215">Les méthodes appelées dans `StopAsync` doivent retourner rapidement.</span><span class="sxs-lookup"><span data-stu-id="01493-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="01493-216">Cependant, les tâches ne sont pas abandonnées après la demande d’annulation : l’appelant attend que toutes les tâches se terminent.</span><span class="sxs-lookup"><span data-stu-id="01493-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="01493-217">Si l’application s’arrête inopinément (par exemple en cas d’échec du processus de l’application), `StopAsync` n’est probablement pas appelée.</span><span class="sxs-lookup"><span data-stu-id="01493-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="01493-218">Par conséquent, les méthodes appelées ou les opérations effectuées dans `StopAsync` peuvent ne pas se produire.</span><span class="sxs-lookup"><span data-stu-id="01493-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="01493-219">Pour prolonger le délai d’expiration par défaut de cinq secondes, définissez :</span><span class="sxs-lookup"><span data-stu-id="01493-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="01493-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quand vous utilisez l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="01493-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="01493-221">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="01493-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="01493-222">Le paramètre de configuration du délai d’expiration de l’hôte quand vous utilisez l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="01493-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="01493-223">Pour plus d’informations, consultez <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="01493-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="01493-224">Le service hébergé est activé une seule fois au démarrage de l’application et s’arrête normalement à l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="01493-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="01493-225">Si une erreur est levée pendant l’exécution des tâches d’arrière-plan, `Dispose` doit être appelée même si `StopAsync` n’est pas appelée.</span><span class="sxs-lookup"><span data-stu-id="01493-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="01493-226">Tâche d’arrière-plan avec minuteur</span><span class="sxs-lookup"><span data-stu-id="01493-226">Timed background tasks</span></span>

<span data-ttu-id="01493-227">Une tâche d’arrière-plan avec minuteur utilise la classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="01493-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="01493-228">Le minuteur déclenche la méthode `DoWork` de la tâche.</span><span class="sxs-lookup"><span data-stu-id="01493-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="01493-229">Le minuteur est désactivé sur `StopAsync` et supprimé quand le conteneur du service est supprimé sur `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="01493-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="01493-230">Le <xref:System.Threading.Timer> n’attend pas pour `DoWork` les exécutions précédentes de se terminer, de sorte que l’approche montrée pourrait ne pas convenir à tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="01493-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="01493-231">Le service est inscrit dans `Startup.ConfigureServices` avec la méthode d’extension `AddHostedService` :</span><span class="sxs-lookup"><span data-stu-id="01493-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="01493-232">Utilisation d’un service délimité dans une tâche d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="01493-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="01493-233">Pour utiliser les services `IHostedService`à [portée](xref:fundamentals/dependency-injection#service-lifetimes) dans un, créer une portée.</span><span class="sxs-lookup"><span data-stu-id="01493-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="01493-234">Par défaut, aucune étendue n’est créée pour un service hébergé.</span><span class="sxs-lookup"><span data-stu-id="01493-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="01493-235">Le service des tâches d’arrière-plan délimitées contient la logique de la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="01493-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="01493-236">Dans l’exemple suivant, un <xref:Microsoft.Extensions.Logging.ILogger> est injecté dans le service :</span><span class="sxs-lookup"><span data-stu-id="01493-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="01493-237">Le service hébergé crée une étendue pour résoudre le service des tâches d’arrière-plan délimitées pour appeler sa méthode `DoWork` :</span><span class="sxs-lookup"><span data-stu-id="01493-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="01493-238">Les services sont inscrits dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="01493-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="01493-239">L’implémentation `IHostedService` est inscrite avec la méthode d’extension `AddHostedService` :</span><span class="sxs-lookup"><span data-stu-id="01493-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="01493-240">Tâches d’arrière-plan en file d’attente</span><span class="sxs-lookup"><span data-stu-id="01493-240">Queued background tasks</span></span>

<span data-ttu-id="01493-241">Une file d’attente de tâches de fond <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> est basée sur le cadre .NET 4.x[(provisoirement prévu pour être intégré pour ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)) :</span><span class="sxs-lookup"><span data-stu-id="01493-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="01493-242">Dans `QueueHostedService`, les tâches d'arrière-plan dans la file d'attente sont retirées de la file d'attente et exécutées en tant que [BackgroundService](#backgroundservice-base-class), qui est une classe de base pour l’implémentation d’une exécution longue `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="01493-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="01493-243">Les services sont inscrits dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="01493-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="01493-244">L’implémentation `IHostedService` est inscrite avec la méthode d’extension `AddHostedService` :</span><span class="sxs-lookup"><span data-stu-id="01493-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="01493-245">Dans la classe de modèle de page Index :</span><span class="sxs-lookup"><span data-stu-id="01493-245">In the Index page model class:</span></span>

* <span data-ttu-id="01493-246">`IBackgroundTaskQueue` est injecté dans le constructeur et affecté à `Queue`.</span><span class="sxs-lookup"><span data-stu-id="01493-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="01493-247">Un <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> est injecté et affecté à `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="01493-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="01493-248">La fabrique est utilisée pour créer des instances de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, qui servent à créer des services au sein d’une étendue.</span><span class="sxs-lookup"><span data-stu-id="01493-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="01493-249">Une étendue est créée de façon à utiliser l’élément `AppDbContext` ([service délimité](xref:fundamentals/dependency-injection#service-lifetimes)) de l’application pour écrire des enregistrements de base de données dans `IBackgroundTaskQueue` (service singleton).</span><span class="sxs-lookup"><span data-stu-id="01493-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="01493-250">Quand le bouton **Ajouter une tâche** est sélectionné dans la page Index, la méthode `OnPostAddTask` est exécutée.</span><span class="sxs-lookup"><span data-stu-id="01493-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="01493-251">`QueueBackgroundWorkItem`est appelé à enqueue un élément de travail:</span><span class="sxs-lookup"><span data-stu-id="01493-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="01493-252">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="01493-252">Additional resources</span></span>

* [<span data-ttu-id="01493-253">Implémenter des tâches d’arrière-plan dans des microservices avec IHostedService et la classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="01493-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="01493-254">Exécuter des tâches en arrière-plan avec WebJobs dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="01493-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
