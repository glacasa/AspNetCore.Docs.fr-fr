---
<span data-ttu-id="8b0dd-101">title : tâches en arrière-plan avec les services hébergés dans ASP.NET Core auteur : Rick-Anderson Description : Apprenez à implémenter des tâches en arrière-plan avec les services hébergés dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-101">title: Background tasks with hosted services in ASP.NET Core author: rick-anderson description: Learn how to implement background tasks with hosted services in ASP.NET Core.</span></span>
<span data-ttu-id="8b0dd-102">monikerRange : ' >= aspnetcore-2,1 'ms. Author : Riande ms. Custom : MVC ms. Date : 02/10/2020 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-102">monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 02/10/2020 no-loc:</span></span>
- <span data-ttu-id="8b0dd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8b0dd-103">'Blazor'</span></span>
- <span data-ttu-id="8b0dd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8b0dd-104">'Identity'</span></span>
- <span data-ttu-id="8b0dd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8b0dd-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8b0dd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8b0dd-106">'Razor'</span></span>
- <span data-ttu-id="8b0dd-107">' SignalR 'UID : notions de base/hôte/hébergé-services</span><span class="sxs-lookup"><span data-stu-id="8b0dd-107">'SignalR' uid: fundamentals/host/hosted-services</span></span>

---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="8b0dd-108">Tâches d’arrière-plan avec des services hébergés dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b0dd-108">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="8b0dd-109">Par [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="8b0dd-109">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b0dd-110">Dans ASP.NET Core, les tâches d’arrière-plan peuvent être implémentées en tant que *services hébergés*.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-110">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="8b0dd-111">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-111">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8b0dd-112">Cette rubrique contient trois exemples de service hébergé :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-112">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="8b0dd-113">Tâche d’arrière-plan qui s’exécute sur un minuteur.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-113">Background task that runs on a timer.</span></span>
* <span data-ttu-id="8b0dd-114">Service hébergé qui active un [service étendu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-114">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8b0dd-115">Le service étendu peut utiliser l' [injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-115">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="8b0dd-116">Tâches d’arrière-plan en file d’attente qui s’exécutent séquentiellement.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-116">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="8b0dd-117">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b0dd-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="8b0dd-118">Modèle Service Worker</span><span class="sxs-lookup"><span data-stu-id="8b0dd-118">Worker Service template</span></span>

<span data-ttu-id="8b0dd-119">Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-119">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="8b0dd-120">Une application créée à partir du modèle de service Worker spécifie le kit de développement logiciel (SDK) Worker dans son fichier projet :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-120">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="8b0dd-121">Pour utiliser le modèle en tant que base d’une application de services hébergés :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="8b0dd-122">Paquet</span><span class="sxs-lookup"><span data-stu-id="8b0dd-122">Package</span></span>

<span data-ttu-id="8b0dd-123">Une application basée sur le modèle de service worker utilise le `Microsoft.NET.Sdk.Worker` Kit de développement logiciel (SDK) et possède une référence de package explicite au package [Microsoft. extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-123">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="8b0dd-124">Par exemple, consultez le fichier projet de l’exemple d’application (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-124">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="8b0dd-125">Pour les applications Web qui utilisent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK), le package [Microsoft. extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) est référencé implicitement à partir de l’infrastructure partagée.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-125">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="8b0dd-126">Une référence de package explicite dans le fichier projet de l’application n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-126">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="8b0dd-127">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="8b0dd-127">IHostedService interface</span></span>

<span data-ttu-id="8b0dd-128">L' <xref:Microsoft.Extensions.Hosting.IHostedService> interface définit deux méthodes pour les objets gérés par l’hôte :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-128">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="8b0dd-129">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contient la logique de démarrage de la tâche en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="8b0dd-130">`StartAsync`est appelé *avant*:</span><span class="sxs-lookup"><span data-stu-id="8b0dd-130">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="8b0dd-131">Le pipeline de traitement des demandes de l’application est configuré ( `Startup.Configure` ).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-131">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="8b0dd-132">Le serveur est démarré et [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-132">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="8b0dd-133">Le comportement par défaut peut être modifié de sorte que le service hébergé `StartAsync` s’exécute après que le pipeline de l’application a été configuré et qu’il `ApplicationStarted` est appelé.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-133">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="8b0dd-134">Pour modifier le comportement par défaut, ajoutez le service hébergé ( `VideosWatcher` dans l’exemple suivant) après l’appel de `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-134">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="8b0dd-135">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): déclenché lorsque l’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="8b0dd-136">`StopAsync` contient la logique pour terminer la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-136">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="8b0dd-137">Implémentez <xref:System.IDisposable> et les [finaliseurs (destructeurs)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pour supprimer toutes les ressources non managées.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-137">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="8b0dd-138">Le jeton d’annulation a un délai d’expiration par défaut de cinq secondes pour indiquer que le processus d’arrêt ne doit plus être normal.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-138">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="8b0dd-139">Quand l’annulation est demandée sur le jeton :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-139">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="8b0dd-140">Les opérations en arrière-plan restantes effectuées par l’application doivent être abandonnées.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-140">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="8b0dd-141">Les méthodes appelées dans `StopAsync` doivent retourner rapidement.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-141">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="8b0dd-142">Cependant, les tâches ne sont pas abandonnées après la demande d’annulation : l’appelant attend que toutes les tâches se terminent.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-142">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="8b0dd-143">Si l’application s’arrête inopinément (par exemple en cas d’échec du processus de l’application), `StopAsync` n’est probablement pas appelée.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-143">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="8b0dd-144">Par conséquent, les méthodes appelées ou les opérations effectuées dans `StopAsync` peuvent ne pas se produire.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-144">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="8b0dd-145">Pour prolonger le délai d’expiration par défaut de cinq secondes, définissez :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-145">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="8b0dd-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quand vous utilisez l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="8b0dd-147">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-147">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="8b0dd-148">Le paramètre de configuration du délai d’expiration de l’hôte quand vous utilisez l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-148">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="8b0dd-149">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-149">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="8b0dd-150">Le service hébergé est activé une seule fois au démarrage de l’application et s’arrête normalement à l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-150">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="8b0dd-151">Si une erreur est levée pendant l’exécution des tâches d’arrière-plan, `Dispose` doit être appelée même si `StopAsync` n’est pas appelée.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-151">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="8b0dd-152">Classe de base BackgroundService</span><span class="sxs-lookup"><span data-stu-id="8b0dd-152">BackgroundService base class</span></span>

<span data-ttu-id="8b0dd-153"><xref:Microsoft.Extensions.Hosting.BackgroundService>est une classe de base pour l’implémentation d’une exécution longue <xref:Microsoft.Extensions.Hosting.IHostedService> .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="8b0dd-154">[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) est appelé pour exécuter le service d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="8b0dd-155">L’implémentation retourne un <xref:System.Threading.Tasks.Task> qui représente la durée de vie totale du service d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-155">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="8b0dd-156">Aucun autre service n’est démarré tant que [ExecuteAsync n’est pas asynchrone](https://github.com/dotnet/extensions/issues/2149), par exemple en appelant `await` .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-156">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="8b0dd-157">Évitez d’effectuer des opérations d’initialisation de blocage longues dans `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-157">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="8b0dd-158">L’hôte bloque dans [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) en attente de la fin de l' `ExecuteAsync` exécution.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-158">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="8b0dd-159">Le jeton d’annulation est déclenché lors de l’appel de [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-159">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="8b0dd-160">Votre implémentation de `ExecuteAsync` doit se terminer rapidement lorsque le jeton d’annulation est déclenché afin d’arrêter correctement le service.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-160">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="8b0dd-161">Dans le cas contraire, le service s’arrête de manière inappropriée au délai d’attente de l’arrêt.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-161">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="8b0dd-162">Pour plus d’informations, consultez la section de l' [interface IHostedService](#ihostedservice-interface) .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-162">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="8b0dd-163">Tâche d’arrière-plan avec minuteur</span><span class="sxs-lookup"><span data-stu-id="8b0dd-163">Timed background tasks</span></span>

<span data-ttu-id="8b0dd-164">Une tâche d’arrière-plan avec minuteur utilise la classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="8b0dd-165">Le minuteur déclenche la méthode `DoWork` de la tâche.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="8b0dd-166">Le minuteur est désactivé sur `StopAsync` et supprimé quand le conteneur du service est supprimé sur `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="8b0dd-167">Le <xref:System.Threading.Timer> n’attend pas que les exécutions précédentes se `DoWork` terminent. l’approche illustrée peut donc ne pas convenir à chaque scénario.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-167">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="8b0dd-168">Avec [verrouillage. l’incrément](xref:System.Threading.Interlocked.Increment*) est utilisé pour incrémenter le compteur d’exécution comme une opération atomique, ce qui garantit que plusieurs threads ne sont pas mis à jour `executionCount` simultanément.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="8b0dd-169">Le service est inscrit dans `IHostBuilder.ConfigureServices` (*Program.cs*) avec la `AddHostedService` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-169">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="8b0dd-170">Utilisation d’un service délimité dans une tâche d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="8b0dd-170">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="8b0dd-171">Pour utiliser les [services délimités](xref:fundamentals/dependency-injection#service-lifetimes) dans un [BackgroundService](#backgroundservice-base-class), créez une étendue.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-171">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="8b0dd-172">Par défaut, aucune étendue n’est créée pour un service hébergé.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-172">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="8b0dd-173">Le service des tâches d’arrière-plan délimitées contient la logique de la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-173">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="8b0dd-174">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-174">In the following example:</span></span>

* <span data-ttu-id="8b0dd-175">Le service est asynchrone.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-175">The service is asynchronous.</span></span> <span data-ttu-id="8b0dd-176">La méthode `DoWork` retourne un `Task`.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-176">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="8b0dd-177">À des fins de démonstration, un délai de dix secondes est attendu dans la `DoWork` méthode.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-177">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="8b0dd-178">Un <xref:Microsoft.Extensions.Logging.ILogger> est injecté dans le service.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-178">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="8b0dd-179">Le service hébergé crée une étendue pour résoudre le service de tâche d’arrière-plan étendu afin d’appeler sa `DoWork` méthode.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-179">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="8b0dd-180">`DoWork`retourne un `Task` , qui est attendu dans `ExecuteAsync` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-180">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="8b0dd-181">Les services sont inscrits dans `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-181">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="8b0dd-182">Le service hébergé est inscrit avec la `AddHostedService` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-182">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="8b0dd-183">Tâches d’arrière-plan en file d’attente</span><span class="sxs-lookup"><span data-stu-id="8b0dd-183">Queued background tasks</span></span>

<span data-ttu-id="8b0dd-184">Une file d’attente de tâches en arrière-plan est basée sur .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-184">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="8b0dd-185">Dans l' `QueueHostedService` exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-185">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="8b0dd-186">La `BackgroundProcessing` méthode retourne un `Task` , qui est attendu dans `ExecuteAsync` .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-186">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="8b0dd-187">Les tâches en arrière-plan de la file d’attente sont déplacées en file d’attente et exécutées dans `BackgroundProcessing` .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-187">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="8b0dd-188">Les éléments de travail sont attendus avant l’arrêt du service dans `StopAsync` .</span><span class="sxs-lookup"><span data-stu-id="8b0dd-188">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="8b0dd-189">Un `MonitorLoop` service gère les tâches de mise en file d’attente pour le service hébergé chaque fois que la `w` clé est sélectionnée sur un appareil d’entrée :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-189">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="8b0dd-190">`IBackgroundTaskQueue`Est injecté dans le `MonitorLoop` service.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-190">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="8b0dd-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem`est appelé pour empiler un élément de travail.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="8b0dd-192">L’élément de travail simule une tâche en arrière-plan de longue durée :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-192">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="8b0dd-193">Trois retards de 5 secondes sont exécutés ( `Task.Delay` ).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-193">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="8b0dd-194">Une `try-catch` instruction intercepte <xref:System.OperationCanceledException> si la tâche est annulée.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-194">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="8b0dd-195">Les services sont inscrits dans `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="8b0dd-196">Le service hébergé est inscrit avec la `AddHostedService` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="8b0dd-197">`MonitorLoop`est démarré dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-197">`MonitorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b0dd-198">Dans ASP.NET Core, les tâches d’arrière-plan peuvent être implémentées en tant que *services hébergés*.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-198">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="8b0dd-199">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-199">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8b0dd-200">Cette rubrique contient trois exemples de service hébergé :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-200">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="8b0dd-201">Tâche d’arrière-plan qui s’exécute sur un minuteur.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-201">Background task that runs on a timer.</span></span>
* <span data-ttu-id="8b0dd-202">Service hébergé qui active un [service étendu](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-202">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8b0dd-203">Le service étendu peut utiliser l' [injection de dépendances (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="8b0dd-203">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="8b0dd-204">Tâches d’arrière-plan en file d’attente qui s’exécutent séquentiellement.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-204">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="8b0dd-205">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b0dd-205">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="8b0dd-206">Paquet</span><span class="sxs-lookup"><span data-stu-id="8b0dd-206">Package</span></span>

<span data-ttu-id="8b0dd-207">Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-207">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="8b0dd-208">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="8b0dd-208">IHostedService interface</span></span>

<span data-ttu-id="8b0dd-209">Les services hébergés implémentent l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-209">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8b0dd-210">L’interface définit deux méthodes pour les objets qui sont gérés par l’hôte :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-210">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="8b0dd-211">[StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contient la logique de démarrage de la tâche en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="8b0dd-212">Lorsque vous utilisez l' [hôte Web](xref:fundamentals/host/web-host), `StartAsync` est appelé après que le serveur a démarré et [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) est déclenché.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-212">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="8b0dd-213">Lors de l’utilisation de l' [hôte générique](xref:fundamentals/host/generic-host), `StartAsync` est appelé avant que `ApplicationStarted` ne soit déclenché.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-213">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="8b0dd-214">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): déclenché lorsque l’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="8b0dd-215">`StopAsync` contient la logique pour terminer la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-215">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="8b0dd-216">Implémentez <xref:System.IDisposable> et les [finaliseurs (destructeurs)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pour supprimer toutes les ressources non managées.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-216">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="8b0dd-217">Le jeton d’annulation a un délai d’expiration par défaut de cinq secondes pour indiquer que le processus d’arrêt ne doit plus être normal.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-217">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="8b0dd-218">Quand l’annulation est demandée sur le jeton :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-218">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="8b0dd-219">Les opérations en arrière-plan restantes effectuées par l’application doivent être abandonnées.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-219">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="8b0dd-220">Les méthodes appelées dans `StopAsync` doivent retourner rapidement.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-220">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="8b0dd-221">Cependant, les tâches ne sont pas abandonnées après la demande d’annulation : l’appelant attend que toutes les tâches se terminent.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-221">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="8b0dd-222">Si l’application s’arrête inopinément (par exemple en cas d’échec du processus de l’application), `StopAsync` n’est probablement pas appelée.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-222">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="8b0dd-223">Par conséquent, les méthodes appelées ou les opérations effectuées dans `StopAsync` peuvent ne pas se produire.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-223">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="8b0dd-224">Pour prolonger le délai d’expiration par défaut de cinq secondes, définissez :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-224">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="8b0dd-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quand vous utilisez l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="8b0dd-226">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-226">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="8b0dd-227">Le paramètre de configuration du délai d’expiration de l’hôte quand vous utilisez l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-227">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="8b0dd-228">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-228">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="8b0dd-229">Le service hébergé est activé une seule fois au démarrage de l’application et s’arrête normalement à l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-229">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="8b0dd-230">Si une erreur est levée pendant l’exécution des tâches d’arrière-plan, `Dispose` doit être appelée même si `StopAsync` n’est pas appelée.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-230">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="8b0dd-231">Tâche d’arrière-plan avec minuteur</span><span class="sxs-lookup"><span data-stu-id="8b0dd-231">Timed background tasks</span></span>

<span data-ttu-id="8b0dd-232">Une tâche d’arrière-plan avec minuteur utilise la classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-232">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="8b0dd-233">Le minuteur déclenche la méthode `DoWork` de la tâche.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-233">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="8b0dd-234">Le minuteur est désactivé sur `StopAsync` et supprimé quand le conteneur du service est supprimé sur `Dispose` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-234">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="8b0dd-235">Le <xref:System.Threading.Timer> n’attend pas que les exécutions précédentes se `DoWork` terminent. l’approche illustrée peut donc ne pas convenir à chaque scénario.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-235">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="8b0dd-236">Le service est inscrit dans `Startup.ConfigureServices` avec la méthode d’extension `AddHostedService` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-236">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="8b0dd-237">Utilisation d’un service délimité dans une tâche d’arrière-plan</span><span class="sxs-lookup"><span data-stu-id="8b0dd-237">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="8b0dd-238">Pour utiliser les [services délimités](xref:fundamentals/dependency-injection#service-lifetimes) dans un `IHostedService` objet, créez une étendue.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-238">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="8b0dd-239">Par défaut, aucune étendue n’est créée pour un service hébergé.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-239">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="8b0dd-240">Le service des tâches d’arrière-plan délimitées contient la logique de la tâche d’arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-240">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="8b0dd-241">Dans l’exemple suivant, un <xref:Microsoft.Extensions.Logging.ILogger> est injecté dans le service :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-241">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="8b0dd-242">Le service hébergé crée une étendue pour résoudre le service des tâches d’arrière-plan délimitées pour appeler sa méthode `DoWork` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-242">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="8b0dd-243">Les services sont inscrits dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b0dd-244">L’implémentation `IHostedService` est inscrite avec la méthode d’extension `AddHostedService` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="8b0dd-245">Tâches d’arrière-plan en file d’attente</span><span class="sxs-lookup"><span data-stu-id="8b0dd-245">Queued background tasks</span></span>

<span data-ttu-id="8b0dd-246">Une file d’attente de tâches en arrière-plan est basée sur le .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([prévu provisoirement pour être intégré pour ASP.net Core](https://github.com/aspnet/Hosting/issues/1280)) :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-246">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="8b0dd-247">Dans `QueueHostedService`, les tâches d'arrière-plan dans la file d'attente sont retirées de la file d'attente et exécutées en tant que [BackgroundService](#backgroundservice-base-class), qui est une classe de base pour l’implémentation d’une exécution longue `IHostedService` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-247">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="8b0dd-248">Les services sont inscrits dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-248">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b0dd-249">L’implémentation `IHostedService` est inscrite avec la méthode d’extension `AddHostedService` :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-249">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="8b0dd-250">Dans la classe de modèle de page Index :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-250">In the Index page model class:</span></span>

* <span data-ttu-id="8b0dd-251">`IBackgroundTaskQueue` est injecté dans le constructeur et affecté à `Queue`.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-251">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="8b0dd-252">Un <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> est injecté et affecté à `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-252">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="8b0dd-253">La fabrique est utilisée pour créer des instances de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, qui servent à créer des services au sein d’une étendue.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-253">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="8b0dd-254">Une étendue est créée de façon à utiliser l’élément `AppDbContext` ([service délimité](xref:fundamentals/dependency-injection#service-lifetimes)) de l’application pour écrire des enregistrements de base de données dans `IBackgroundTaskQueue` (service singleton).</span><span class="sxs-lookup"><span data-stu-id="8b0dd-254">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="8b0dd-255">Quand le bouton **Ajouter une tâche** est sélectionné dans la page Index, la méthode `OnPostAddTask` est exécutée.</span><span class="sxs-lookup"><span data-stu-id="8b0dd-255">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="8b0dd-256">`QueueBackgroundWorkItem`est appelé pour empiler un élément de travail :</span><span class="sxs-lookup"><span data-stu-id="8b0dd-256">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8b0dd-257">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8b0dd-257">Additional resources</span></span>

* [<span data-ttu-id="8b0dd-258">Implémenter des tâches d’arrière-plan dans des microservices avec IHostedService et la classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="8b0dd-258">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="8b0dd-259">Exécuter des tâches en arrière-plan avec WebJobs dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8b0dd-259">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
