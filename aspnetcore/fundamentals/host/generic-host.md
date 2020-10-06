---
title: Hôte générique .NET dans ASP.NET Core
author: rick-anderson
description: Utilisez l’hôte générique .NET Core dans les applications ASP.NET Core.  L’hôte générique est responsable du démarrage de l’application et de la gestion de la durée de vie.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762319"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="d471d-104">Hôte générique .NET dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d471d-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d471d-105">Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="d471d-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="d471d-106">Cette rubrique fournit des informations sur l’utilisation de l’hôte générique .NET dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d471d-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="d471d-107">Pour plus d’informations sur l’utilisation de l’hôte générique .NET dans les applications de console, consultez [hôte générique .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d471d-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d471d-108">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-108">Host definition</span></span>

<span data-ttu-id="d471d-109">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="d471d-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d471d-110">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="d471d-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="d471d-111">Journalisation</span><span class="sxs-lookup"><span data-stu-id="d471d-111">Logging</span></span>
* <span data-ttu-id="d471d-112">Configuration</span><span class="sxs-lookup"><span data-stu-id="d471d-112">Configuration</span></span>
* <span data-ttu-id="d471d-113">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="d471d-113">`IHostedService` implementations</span></span>

<span data-ttu-id="d471d-114">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d471d-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d471d-115">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="d471d-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d471d-116">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d471d-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d471d-117">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-117">Set up a host</span></span>

<span data-ttu-id="d471d-118">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="d471d-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d471d-119">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="d471d-119">The `Main` method:</span></span>

* <span data-ttu-id="d471d-120">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="d471d-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d471d-121">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="d471d-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d471d-122">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="d471d-123">Le code suivant crée une charge de travail non-HTTP avec une `IHostedService` implémentation ajoutée au conteneur di.</span><span class="sxs-lookup"><span data-stu-id="d471d-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d471d-124">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d471d-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d471d-125">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d471d-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d471d-126">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d471d-127">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d471d-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d471d-128">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="d471d-128">Default builder settings</span></span>

<span data-ttu-id="d471d-129">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="d471d-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="d471d-130">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="d471d-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="d471d-131">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="d471d-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="d471d-132">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="d471d-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d471d-133">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d471d-133">Command-line arguments.</span></span>
* <span data-ttu-id="d471d-134">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="d471d-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="d471d-135">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="d471d-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="d471d-136">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="d471d-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d471d-137">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="d471d-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d471d-138">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-138">Environment variables.</span></span>
  * <span data-ttu-id="d471d-139">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d471d-139">Command-line arguments.</span></span>
* <span data-ttu-id="d471d-140">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="d471d-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d471d-141">Console</span><span class="sxs-lookup"><span data-stu-id="d471d-141">Console</span></span>
  * <span data-ttu-id="d471d-142">Débogage</span><span class="sxs-lookup"><span data-stu-id="d471d-142">Debug</span></span>
  * <span data-ttu-id="d471d-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="d471d-143">EventSource</span></span>
  * <span data-ttu-id="d471d-144">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="d471d-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d471d-145">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="d471d-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d471d-146">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d471d-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d471d-147">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="d471d-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d471d-148">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d471d-149">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d471d-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d471d-150">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d471d-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d471d-151">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="d471d-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d471d-152">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="d471d-152">Enables IIS integration.</span></span> <span data-ttu-id="d471d-153">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d471d-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d471d-154">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d471d-155">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="d471d-155">Framework-provided services</span></span>

<span data-ttu-id="d471d-156">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="d471d-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d471d-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d471d-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d471d-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d471d-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d471d-160">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="d471d-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d471d-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="d471d-162">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d471d-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d471d-163">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d471d-164">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="d471d-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d471d-165">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="d471d-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d471d-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-166">IHostLifetime</span></span>

<span data-ttu-id="d471d-167">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d471d-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d471d-168">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d471d-168">The last implementation registered is used.</span></span>

<span data-ttu-id="d471d-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d471d-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d471d-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d471d-171">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d471d-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="d471d-172">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d471d-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d471d-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d471d-173">IHostEnvironment</span></span>

<span data-ttu-id="d471d-174">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="d471d-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d471d-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d471d-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d471d-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d471d-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d471d-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d471d-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d471d-178">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="d471d-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d471d-179">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-179">Host configuration</span></span>

<span data-ttu-id="d471d-180">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d471d-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d471d-181">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="d471d-182">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d471d-183">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d471d-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d471d-184">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d471d-185">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d471d-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d471d-186">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d471d-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d471d-187">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d471d-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d471d-188">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d471d-189">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="d471d-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d471d-190">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d471d-191">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="d471d-191">App configuration</span></span>

<span data-ttu-id="d471d-192">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d471d-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d471d-193">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d471d-194">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d471d-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d471d-195">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d471d-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d471d-196">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d471d-197">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d471d-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d471d-198">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="d471d-198">Settings for all app types</span></span>

<span data-ttu-id="d471d-199">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d471d-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d471d-200">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d471d-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d471d-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d471d-201">ApplicationName</span></span>

<span data-ttu-id="d471d-202">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d471d-203">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d471d-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d471d-204">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-204">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-205">**Valeur par défaut**: nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d471d-206">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d471d-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d471d-207">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d471d-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-208">ContentRoot</span></span>

<span data-ttu-id="d471d-209">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="d471d-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d471d-210">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="d471d-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d471d-211">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d471d-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d471d-212">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-212">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-213">**Par défaut**: le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d471d-214">**Variable d’environnement**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d471d-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d471d-215">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d471d-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d471d-216">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d471d-216">For more information, see:</span></span>

* [<span data-ttu-id="d471d-217">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="d471d-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d471d-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d471d-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d471d-219">EnvironmentName</span></span>

<span data-ttu-id="d471d-220">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="d471d-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d471d-221">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="d471d-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d471d-222">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="d471d-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d471d-223">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d471d-223">**Key**: `environment`</span></span>  
<span data-ttu-id="d471d-224">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-224">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-225">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d471d-225">**Default**: `Production`</span></span>  
<span data-ttu-id="d471d-226">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d471d-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d471d-227">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d471d-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d471d-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d471d-228">ShutdownTimeout</span></span>

<span data-ttu-id="d471d-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d471d-230">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="d471d-230">The default value is five seconds.</span></span>  <span data-ttu-id="d471d-231">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="d471d-232">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d471d-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d471d-233">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="d471d-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d471d-234">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d471d-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d471d-235">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="d471d-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d471d-236">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="d471d-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d471d-237">**Clé** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d471d-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d471d-238">**Type** : `int`</span><span class="sxs-lookup"><span data-stu-id="d471d-238">**Type**: `int`</span></span>  
<span data-ttu-id="d471d-239">**Valeur par défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="d471d-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d471d-240">**Variable d’environnement**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d471d-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d471d-241">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="d471d-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d471d-242">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="d471d-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="d471d-243">Désactiver le rechargement de la configuration d’application lors de la modification</span><span class="sxs-lookup"><span data-stu-id="d471d-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="d471d-244">Par [défaut](xref:fundamentals/configuration/index#default), *appsettings.jssur* et *appSettings. { Environment}. JSON* est rechargé lorsque le fichier change.</span><span class="sxs-lookup"><span data-stu-id="d471d-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="d471d-245">Pour désactiver ce comportement de rechargement dans ASP.NET Core 5,0 ou version ultérieure, affectez la valeur `hostBuilder:reloadConfigOnChange` à la clé `false` .</span><span class="sxs-lookup"><span data-stu-id="d471d-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="d471d-246">**Clé** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d471d-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d471d-247">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-248">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="d471d-248">**Default**: `true`</span></span>  
<span data-ttu-id="d471d-249">**Argument de ligne de commande**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d471d-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d471d-250">**Variable d’environnement**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d471d-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="d471d-251">Le séparateur deux `:` -points () ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="d471d-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="d471d-252">Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="d471d-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="d471d-253">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="d471d-253">Settings for web apps</span></span>

<span data-ttu-id="d471d-254">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="d471d-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d471d-255">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d471d-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d471d-256">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="d471d-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d471d-257">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d471d-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d471d-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d471d-258">CaptureStartupErrors</span></span>

<span data-ttu-id="d471d-259">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d471d-260">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="d471d-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d471d-261">**Clé** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d471d-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d471d-262">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-263">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="d471d-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d471d-264">**Variable d’environnement**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d471d-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d471d-265">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="d471d-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d471d-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d471d-266">DetailedErrors</span></span>

<span data-ttu-id="d471d-267">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="d471d-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d471d-268">**Clé** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d471d-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d471d-269">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-270">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d471d-270">**Default**: `false`</span></span>  
<span data-ttu-id="d471d-271">**Variable d’environnement**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d471d-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d471d-272">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d471d-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d471d-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="d471d-274">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d471d-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d471d-275">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d471d-276">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d471d-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d471d-277">**Clé** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d471d-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d471d-278">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-278">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-279">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d471d-279">**Default**: Empty string</span></span>  
<span data-ttu-id="d471d-280">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d471d-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d471d-281">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d471d-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d471d-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d471d-283">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d471d-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d471d-284">**Clé** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d471d-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d471d-285">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-285">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-286">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d471d-286">**Default**: Empty string</span></span>  
<span data-ttu-id="d471d-287">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d471d-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d471d-288">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d471d-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d471d-289">HTTPS_Port</span></span>

<span data-ttu-id="d471d-290">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d471d-290">The HTTPS redirect port.</span></span> <span data-ttu-id="d471d-291">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d471d-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d471d-292">**Clé** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="d471d-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="d471d-293">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-293">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-294">Valeur **par défaut**: aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="d471d-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d471d-295">**Variable d’environnement**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d471d-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d471d-296">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d471d-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d471d-297">PreferHostingUrls</span></span>

<span data-ttu-id="d471d-298">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="d471d-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d471d-299">**Clé** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d471d-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d471d-300">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-301">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="d471d-301">**Default**: `true`</span></span>  
<span data-ttu-id="d471d-302">**Variable d’environnement**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d471d-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d471d-303">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="d471d-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d471d-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d471d-304">PreventHostingStartup</span></span>

<span data-ttu-id="d471d-305">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d471d-306">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d471d-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d471d-307">**Clé** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d471d-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d471d-308">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-309">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d471d-309">**Default**: `false`</span></span>  
<span data-ttu-id="d471d-310">**Variable d’environnement**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d471d-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d471d-311">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d471d-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d471d-312">StartupAssembly</span></span>

<span data-ttu-id="d471d-313">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d471d-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d471d-314">**Clé** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d471d-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d471d-315">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-315">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-316">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="d471d-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d471d-317">**Variable d’environnement**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d471d-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d471d-318">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="d471d-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d471d-319">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="d471d-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d471d-320">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d471d-321">URLs</span><span class="sxs-lookup"><span data-stu-id="d471d-321">URLs</span></span>

<span data-ttu-id="d471d-322">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="d471d-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d471d-323">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d471d-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d471d-324">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d471d-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d471d-325">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="d471d-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d471d-326">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="d471d-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="d471d-327">**Clé** : `urls`</span><span class="sxs-lookup"><span data-stu-id="d471d-327">**Key**: `urls`</span></span>  
<span data-ttu-id="d471d-328">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-328">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-329">**Par défaut**: `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d471d-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d471d-330">**Variable d’environnement**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d471d-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d471d-331">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="d471d-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d471d-332">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="d471d-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d471d-333">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d471d-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d471d-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-334">WebRoot</span></span>

<span data-ttu-id="d471d-335">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d471d-336">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="d471d-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d471d-337">**Clé** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="d471d-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="d471d-338">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-338">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-339">**Valeur par défaut**: la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="d471d-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d471d-340">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="d471d-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d471d-341">**Variable d’environnement**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d471d-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d471d-342">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d471d-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d471d-343">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d471d-343">For more information, see:</span></span>

* [<span data-ttu-id="d471d-344">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="d471d-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d471d-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d471d-346">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-346">Manage the host lifetime</span></span>

<span data-ttu-id="d471d-347">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d471d-348">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d471d-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d471d-349">Exécuter</span><span class="sxs-lookup"><span data-stu-id="d471d-349">Run</span></span>

<span data-ttu-id="d471d-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d471d-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-351">RunAsync</span></span>

<span data-ttu-id="d471d-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d471d-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d471d-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-353">RunConsoleAsync</span></span>

<span data-ttu-id="d471d-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d471d-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d471d-355">Démarrer</span><span class="sxs-lookup"><span data-stu-id="d471d-355">Start</span></span>

<span data-ttu-id="d471d-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="d471d-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d471d-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-357">StartAsync</span></span>

<span data-ttu-id="d471d-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d471d-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d471d-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="d471d-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d471d-360">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="d471d-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d471d-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-361">StopAsync</span></span>

<span data-ttu-id="d471d-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="d471d-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d471d-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d471d-363">WaitForShutdown</span></span>

<span data-ttu-id="d471d-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d471d-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d471d-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="d471d-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d471d-367">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="d471d-367">External control</span></span>

<span data-ttu-id="d471d-368">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="d471d-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d471d-369">Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="d471d-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="d471d-370">Cette rubrique fournit des informations sur l’utilisation de l’hôte générique .NET dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d471d-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="d471d-371">Pour plus d’informations sur l’utilisation de l’hôte générique .NET dans les applications de console, consultez [hôte générique .net](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d471d-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d471d-372">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-372">Host definition</span></span>

<span data-ttu-id="d471d-373">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="d471d-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d471d-374">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="d471d-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="d471d-375">Journalisation</span><span class="sxs-lookup"><span data-stu-id="d471d-375">Logging</span></span>
* <span data-ttu-id="d471d-376">Configuration</span><span class="sxs-lookup"><span data-stu-id="d471d-376">Configuration</span></span>
* <span data-ttu-id="d471d-377">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="d471d-377">`IHostedService` implementations</span></span>

<span data-ttu-id="d471d-378">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d471d-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d471d-379">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="d471d-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d471d-380">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d471d-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d471d-381">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-381">Set up a host</span></span>

<span data-ttu-id="d471d-382">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="d471d-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d471d-383">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="d471d-383">The `Main` method:</span></span>

* <span data-ttu-id="d471d-384">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="d471d-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d471d-385">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="d471d-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d471d-386">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte générique :</span><span class="sxs-lookup"><span data-stu-id="d471d-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="d471d-387">Le code suivant crée un hôte générique à l’aide d’une charge de travail non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d471d-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="d471d-388">L' `IHostedService` implémentation est ajoutée au conteneur di :</span><span class="sxs-lookup"><span data-stu-id="d471d-388">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d471d-389">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d471d-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d471d-390">Le code précédent est généré par les modèles ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d471d-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="d471d-391">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d471d-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d471d-392">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d471d-393">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d471d-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d471d-394">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="d471d-394">Default builder settings</span></span>

<span data-ttu-id="d471d-395">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> :</span><span class="sxs-lookup"><span data-stu-id="d471d-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="d471d-396">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="d471d-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="d471d-397">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="d471d-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="d471d-398">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="d471d-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d471d-399">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d471d-399">Command-line arguments.</span></span>
* <span data-ttu-id="d471d-400">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="d471d-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="d471d-401">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="d471d-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="d471d-402">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="d471d-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d471d-403">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="d471d-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d471d-404">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-404">Environment variables.</span></span>
  * <span data-ttu-id="d471d-405">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d471d-405">Command-line arguments.</span></span>
* <span data-ttu-id="d471d-406">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="d471d-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d471d-407">Console</span><span class="sxs-lookup"><span data-stu-id="d471d-407">Console</span></span>
  * <span data-ttu-id="d471d-408">Débogage</span><span class="sxs-lookup"><span data-stu-id="d471d-408">Debug</span></span>
  * <span data-ttu-id="d471d-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="d471d-409">EventSource</span></span>
  * <span data-ttu-id="d471d-410">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="d471d-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d471d-411">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="d471d-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d471d-412">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d471d-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d471d-413">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="d471d-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d471d-414">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d471d-415">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d471d-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d471d-416">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d471d-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d471d-417">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="d471d-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d471d-418">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="d471d-418">Enables IIS integration.</span></span> <span data-ttu-id="d471d-419">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d471d-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d471d-420">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d471d-421">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="d471d-421">Framework-provided services</span></span>

<span data-ttu-id="d471d-422">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="d471d-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d471d-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d471d-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d471d-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d471d-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d471d-426">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="d471d-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d471d-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="d471d-428">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d471d-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d471d-429">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d471d-430">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="d471d-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d471d-431">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="d471d-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d471d-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-432">IHostLifetime</span></span>

<span data-ttu-id="d471d-433">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d471d-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d471d-434">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d471d-434">The last implementation registered is used.</span></span>

<span data-ttu-id="d471d-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d471d-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d471d-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d471d-437">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d471d-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="d471d-438">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d471d-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d471d-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d471d-439">IHostEnvironment</span></span>

<span data-ttu-id="d471d-440">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="d471d-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d471d-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d471d-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d471d-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d471d-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d471d-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d471d-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d471d-444">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="d471d-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d471d-445">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-445">Host configuration</span></span>

<span data-ttu-id="d471d-446">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d471d-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d471d-447">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="d471d-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="d471d-448">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d471d-449">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d471d-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="d471d-450">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d471d-451">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d471d-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d471d-452">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d471d-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d471d-453">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d471d-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d471d-454">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d471d-455">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="d471d-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d471d-456">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d471d-457">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="d471d-457">App configuration</span></span>

<span data-ttu-id="d471d-458">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d471d-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d471d-459">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d471d-460">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d471d-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d471d-461">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d471d-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d471d-462">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d471d-463">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d471d-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d471d-464">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="d471d-464">Settings for all app types</span></span>

<span data-ttu-id="d471d-465">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d471d-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d471d-466">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d471d-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d471d-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d471d-467">ApplicationName</span></span>

<span data-ttu-id="d471d-468">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d471d-469">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d471d-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d471d-470">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-470">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-471">**Valeur par défaut**: nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d471d-472">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d471d-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d471d-473">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d471d-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-474">ContentRoot</span></span>

<span data-ttu-id="d471d-475">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="d471d-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d471d-476">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="d471d-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d471d-477">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d471d-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d471d-478">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-478">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-479">**Par défaut**: le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d471d-480">**Variable d’environnement**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d471d-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d471d-481">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d471d-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d471d-482">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d471d-482">For more information, see:</span></span>

* [<span data-ttu-id="d471d-483">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="d471d-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d471d-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d471d-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d471d-485">EnvironmentName</span></span>

<span data-ttu-id="d471d-486">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="d471d-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d471d-487">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="d471d-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d471d-488">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="d471d-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d471d-489">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d471d-489">**Key**: `environment`</span></span>  
<span data-ttu-id="d471d-490">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-490">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-491">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d471d-491">**Default**: `Production`</span></span>  
<span data-ttu-id="d471d-492">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d471d-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d471d-493">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d471d-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d471d-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d471d-494">ShutdownTimeout</span></span>

<span data-ttu-id="d471d-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d471d-496">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="d471d-496">The default value is five seconds.</span></span>  <span data-ttu-id="d471d-497">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="d471d-498">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d471d-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d471d-499">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="d471d-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d471d-500">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d471d-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d471d-501">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="d471d-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d471d-502">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="d471d-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d471d-503">**Clé** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d471d-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d471d-504">**Type** : `int`</span><span class="sxs-lookup"><span data-stu-id="d471d-504">**Type**: `int`</span></span>  
<span data-ttu-id="d471d-505">**Valeur par défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="d471d-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d471d-506">**Variable d’environnement**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d471d-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d471d-507">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="d471d-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d471d-508">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="d471d-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="d471d-509">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="d471d-509">Settings for web apps</span></span>

<span data-ttu-id="d471d-510">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="d471d-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d471d-511">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d471d-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d471d-512">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="d471d-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d471d-513">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d471d-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d471d-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d471d-514">CaptureStartupErrors</span></span>

<span data-ttu-id="d471d-515">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d471d-516">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="d471d-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d471d-517">**Clé** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d471d-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d471d-518">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-519">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="d471d-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d471d-520">**Variable d’environnement**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d471d-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d471d-521">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="d471d-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d471d-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d471d-522">DetailedErrors</span></span>

<span data-ttu-id="d471d-523">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="d471d-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d471d-524">**Clé** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d471d-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d471d-525">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-526">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d471d-526">**Default**: `false`</span></span>  
<span data-ttu-id="d471d-527">**Variable d’environnement**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d471d-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d471d-528">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d471d-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d471d-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="d471d-530">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d471d-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d471d-531">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d471d-532">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d471d-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d471d-533">**Clé** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d471d-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d471d-534">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-534">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-535">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d471d-535">**Default**: Empty string</span></span>  
<span data-ttu-id="d471d-536">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d471d-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d471d-537">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d471d-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d471d-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d471d-539">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d471d-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d471d-540">**Clé** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d471d-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d471d-541">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-541">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-542">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d471d-542">**Default**: Empty string</span></span>  
<span data-ttu-id="d471d-543">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d471d-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d471d-544">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d471d-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d471d-545">HTTPS_Port</span></span>

<span data-ttu-id="d471d-546">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d471d-546">The HTTPS redirect port.</span></span> <span data-ttu-id="d471d-547">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d471d-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d471d-548">**Clé** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="d471d-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="d471d-549">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-549">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-550">Valeur **par défaut**: aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="d471d-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d471d-551">**Variable d’environnement**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d471d-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d471d-552">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d471d-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d471d-553">PreferHostingUrls</span></span>

<span data-ttu-id="d471d-554">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="d471d-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d471d-555">**Clé** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d471d-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d471d-556">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-557">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="d471d-557">**Default**: `true`</span></span>  
<span data-ttu-id="d471d-558">**Variable d’environnement**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d471d-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d471d-559">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="d471d-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d471d-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d471d-560">PreventHostingStartup</span></span>

<span data-ttu-id="d471d-561">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d471d-562">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d471d-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d471d-563">**Clé** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d471d-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d471d-564">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d471d-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d471d-565">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d471d-565">**Default**: `false`</span></span>  
<span data-ttu-id="d471d-566">**Variable d’environnement**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d471d-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d471d-567">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d471d-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d471d-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d471d-568">StartupAssembly</span></span>

<span data-ttu-id="d471d-569">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d471d-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d471d-570">**Clé** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d471d-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d471d-571">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-571">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-572">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="d471d-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d471d-573">**Variable d’environnement**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d471d-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d471d-574">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="d471d-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d471d-575">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="d471d-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d471d-576">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d471d-577">URLs</span><span class="sxs-lookup"><span data-stu-id="d471d-577">URLs</span></span>

<span data-ttu-id="d471d-578">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="d471d-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d471d-579">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d471d-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d471d-580">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d471d-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d471d-581">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="d471d-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d471d-582">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="d471d-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="d471d-583">**Clé** : `urls`</span><span class="sxs-lookup"><span data-stu-id="d471d-583">**Key**: `urls`</span></span>  
<span data-ttu-id="d471d-584">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-584">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-585">**Par défaut**: `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d471d-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d471d-586">**Variable d’environnement**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d471d-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d471d-587">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="d471d-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d471d-588">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="d471d-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d471d-589">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d471d-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d471d-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-590">WebRoot</span></span>

<span data-ttu-id="d471d-591">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d471d-592">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="d471d-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d471d-593">**Clé** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="d471d-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="d471d-594">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-594">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-595">**Valeur par défaut**: la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="d471d-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d471d-596">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="d471d-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d471d-597">**Variable d’environnement**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d471d-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d471d-598">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d471d-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d471d-599">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d471d-599">For more information, see:</span></span>

* [<span data-ttu-id="d471d-600">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="d471d-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d471d-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d471d-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d471d-602">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-602">Manage the host lifetime</span></span>

<span data-ttu-id="d471d-603">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d471d-604">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d471d-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d471d-605">Exécuter</span><span class="sxs-lookup"><span data-stu-id="d471d-605">Run</span></span>

<span data-ttu-id="d471d-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d471d-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-607">RunAsync</span></span>

<span data-ttu-id="d471d-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d471d-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d471d-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-609">RunConsoleAsync</span></span>

<span data-ttu-id="d471d-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d471d-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d471d-611">Démarrer</span><span class="sxs-lookup"><span data-stu-id="d471d-611">Start</span></span>

<span data-ttu-id="d471d-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="d471d-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d471d-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-613">StartAsync</span></span>

<span data-ttu-id="d471d-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d471d-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d471d-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="d471d-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d471d-616">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="d471d-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d471d-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-617">StopAsync</span></span>

<span data-ttu-id="d471d-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="d471d-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d471d-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d471d-619">WaitForShutdown</span></span>

<span data-ttu-id="d471d-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d471d-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d471d-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="d471d-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d471d-623">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="d471d-623">External control</span></span>

<span data-ttu-id="d471d-624">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="d471d-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d471d-625">Les applications ASP.NET Core configurent et lancent un hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d471d-626">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="d471d-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d471d-627">Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d471d-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="d471d-628">L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d471d-629">La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="d471d-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d471d-630">L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web.</span><span class="sxs-lookup"><span data-stu-id="d471d-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="d471d-631">Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="d471d-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d471d-632">L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d471d-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="d471d-633">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d471d-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d471d-634">Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré*.</span><span class="sxs-lookup"><span data-stu-id="d471d-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="d471d-635">N’exécutez pas l’exemple dans une `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="d471d-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="d471d-636">Pour définir la console dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="d471d-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="d471d-637">Ouvrez le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="d471d-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="d471d-638">Dans la configuration **.NET Core Launch (console)**, recherchez l’entrée **console**.</span><span class="sxs-lookup"><span data-stu-id="d471d-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="d471d-639">Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="d471d-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="d471d-640">Introduction</span><span class="sxs-lookup"><span data-stu-id="d471d-640">Introduction</span></span>

<span data-ttu-id="d471d-641">La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="d471d-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="d471d-642">Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).</span><span class="sxs-lookup"><span data-stu-id="d471d-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="d471d-643"><xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code.</span><span class="sxs-lookup"><span data-stu-id="d471d-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="d471d-644">Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="d471d-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="d471d-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.</span><span class="sxs-lookup"><span data-stu-id="d471d-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d471d-646">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-646">Set up a host</span></span>

<span data-ttu-id="d471d-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="d471d-648">Options</span><span class="sxs-lookup"><span data-stu-id="d471d-648">Options</span></span>

<span data-ttu-id="d471d-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="d471d-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d471d-650">Délai d’expiration</span><span class="sxs-lookup"><span data-stu-id="d471d-650">Shutdown timeout</span></span>

<span data-ttu-id="d471d-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d471d-652">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="d471d-652">The default value is five seconds.</span></span>

<span data-ttu-id="d471d-653">La configuration d’option suivante dans `Program.Main` augmente le délai d’attente d’arrêt de cinq secondes par défaut à 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="d471d-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="d471d-654">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="d471d-654">Default services</span></span>

<span data-ttu-id="d471d-655">Les services suivants sont inscrits au moment de l’initialisation de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="d471d-656">[Environnement](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="d471d-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="d471d-657">[Configuration](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="d471d-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="d471d-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d471d-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="d471d-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d471d-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="d471d-660">[Options](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="d471d-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="d471d-661">[Journalisation](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="d471d-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d471d-662">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-662">Host configuration</span></span>

<span data-ttu-id="d471d-663">La création de la configuration d’hôte fait appel aux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="d471d-663">Host configuration is created by:</span></span>

* <span data-ttu-id="d471d-664">Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="d471d-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="d471d-665">Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="d471d-666">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="d471d-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d471d-667">Clé d’application (nom)</span><span class="sxs-lookup"><span data-stu-id="d471d-667">Application key (name)</span></span>

<span data-ttu-id="d471d-668">La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="d471d-669">Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :</span><span class="sxs-lookup"><span data-stu-id="d471d-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="d471d-670">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d471d-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d471d-671">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-671">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-672">**Par défaut** : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d471d-673">**Définir à l’aide**de : `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="d471d-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="d471d-674">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d471d-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="d471d-675">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="d471d-675">Content root</span></span>

<span data-ttu-id="d471d-676">Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="d471d-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="d471d-677">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d471d-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d471d-678">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-678">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-679">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d471d-680">**Définir à l’aide**de : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d471d-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="d471d-681">**Variable d’environnement**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d471d-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d471d-682">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="d471d-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="d471d-683">Pour plus d’informations, consultez [principes de base : racine du contenu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="d471d-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="d471d-684">Environnement</span><span class="sxs-lookup"><span data-stu-id="d471d-684">Environment</span></span>

<span data-ttu-id="d471d-685">Définit l' [environnement](xref:fundamentals/environments)de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d471d-686">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d471d-686">**Key**: `environment`</span></span>  
<span data-ttu-id="d471d-687">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d471d-687">**Type**: `string`</span></span>  
<span data-ttu-id="d471d-688">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d471d-688">**Default**: `Production`</span></span>  
<span data-ttu-id="d471d-689">**Définir à l’aide**de : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d471d-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="d471d-690">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d471d-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d471d-691">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="d471d-691">The environment can be set to any value.</span></span> <span data-ttu-id="d471d-692">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="d471d-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d471d-693">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="d471d-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="d471d-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="d471d-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="d471d-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="d471d-696">La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="d471d-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d471d-698">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d471d-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d471d-699">Aucun fournisseur n’est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-699">No providers are included by default.</span></span> <span data-ttu-id="d471d-700">Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :</span><span class="sxs-lookup"><span data-stu-id="d471d-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="d471d-701">Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="d471d-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="d471d-702">Configuration de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-702">Environment variable configuration.</span></span>
* <span data-ttu-id="d471d-703">Configuration de l’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="d471d-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="d471d-704">Tout autre fournisseur de configuration obligatoire.</span><span class="sxs-lookup"><span data-stu-id="d471d-704">Any other required configuration providers.</span></span>

<span data-ttu-id="d471d-705">Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d471d-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="d471d-706">L’exemple d’application utilise un fichier JSON, *hostsettings.json*, et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.</span><span class="sxs-lookup"><span data-stu-id="d471d-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="d471d-707">Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte.</span><span class="sxs-lookup"><span data-stu-id="d471d-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="d471d-708">`AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d471d-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="d471d-709">L’exemple d’application utilise un préfixe `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="d471d-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="d471d-710">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d471d-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d471d-711">Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="d471d-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d471d-712">Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d471d-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="d471d-713">Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="d471d-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="d471d-714">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d471d-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d471d-715">Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="d471d-716">La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.</span><span class="sxs-lookup"><span data-stu-id="d471d-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="d471d-717">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d471d-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="d471d-718">Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="d471d-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="d471d-719">Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="d471d-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="d471d-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d471d-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d471d-721">Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d471d-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="d471d-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="d471d-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d471d-724">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d471d-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="d471d-725">La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="d471d-726">La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d471d-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="d471d-727">Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="d471d-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="d471d-728">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="d471d-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="d471d-729">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="d471d-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="d471d-730">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="d471d-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="d471d-731">Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="d471d-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="d471d-732">L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :</span><span class="sxs-lookup"><span data-stu-id="d471d-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="d471d-733">Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="d471d-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="d471d-734">Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="d471d-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="d471d-735">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d471d-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="d471d-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d471d-736">ConfigureServices</span></span>

<span data-ttu-id="d471d-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d471d-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d471d-739">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d471d-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d471d-740">Pour plus d’informations, consultez <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d471d-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="d471d-741">L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :</span><span class="sxs-lookup"><span data-stu-id="d471d-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="d471d-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="d471d-742">ConfigureLogging</span></span>

<span data-ttu-id="d471d-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni.</span><span class="sxs-lookup"><span data-stu-id="d471d-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="d471d-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="d471d-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-745">UseConsoleLifetime</span></span>

<span data-ttu-id="d471d-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d471d-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="d471d-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d471d-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="d471d-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut.</span><span class="sxs-lookup"><span data-stu-id="d471d-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="d471d-749">La dernière durée de vie inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d471d-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="d471d-750">Configuration du conteneur</span><span class="sxs-lookup"><span data-stu-id="d471d-750">Container configuration</span></span>

<span data-ttu-id="d471d-751">Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="d471d-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="d471d-752">L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret.</span><span class="sxs-lookup"><span data-stu-id="d471d-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="d471d-753">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="d471d-754">La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="d471d-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="d471d-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="d471d-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d471d-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d471d-757">Créer un conteneur de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="d471d-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="d471d-758">Fournir une fabrique de conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="d471d-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="d471d-759">Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :</span><span class="sxs-lookup"><span data-stu-id="d471d-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="d471d-760">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="d471d-760">Extensibility</span></span>

<span data-ttu-id="d471d-761">L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d471d-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="d471d-762">L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d471d-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="d471d-763">Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :</span><span class="sxs-lookup"><span data-stu-id="d471d-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="d471d-764">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="d471d-764">Manage the host</span></span>

<span data-ttu-id="d471d-765">L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d471d-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d471d-766">Exécuter</span><span class="sxs-lookup"><span data-stu-id="d471d-766">Run</span></span>

<span data-ttu-id="d471d-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d471d-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="d471d-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-768">RunAsync</span></span>

<span data-ttu-id="d471d-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :</span><span class="sxs-lookup"><span data-stu-id="d471d-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="d471d-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-770">RunConsoleAsync</span></span>

<span data-ttu-id="d471d-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d471d-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="d471d-772">Start et StopAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-772">Start and StopAsync</span></span>

<span data-ttu-id="d471d-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="d471d-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="d471d-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="d471d-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="d471d-775">StartAsync et StopAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="d471d-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="d471d-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="d471d-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d471d-778">WaitForShutdown</span></span>

<span data-ttu-id="d471d-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché via le <xref:Microsoft.Extensions.Hosting.IHostLifetime> , par exemple `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (écoute la <kbd>touche Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d471d-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="d471d-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="d471d-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d471d-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="d471d-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d471d-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="d471d-783">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="d471d-783">External control</span></span>

<span data-ttu-id="d471d-784">Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="d471d-784">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="d471d-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="d471d-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d471d-786">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="d471d-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d471d-787">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="d471d-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="d471d-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="d471d-789">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="d471d-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="d471d-790">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d471d-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d471d-791">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d471d-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="d471d-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="d471d-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="d471d-793">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d471d-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d471d-794">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="d471d-794">Cancellation Token</span></span> | <span data-ttu-id="d471d-795">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="d471d-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="d471d-796">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="d471d-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="d471d-797">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d471d-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d471d-798">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="d471d-798">All requests should be processed.</span></span> <span data-ttu-id="d471d-799">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="d471d-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="d471d-800">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d471d-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d471d-801">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="d471d-801">Requests may still be processing.</span></span> <span data-ttu-id="d471d-802">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="d471d-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="d471d-803">Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe.</span><span class="sxs-lookup"><span data-stu-id="d471d-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="d471d-804">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.</span><span class="sxs-lookup"><span data-stu-id="d471d-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="d471d-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="d471d-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="d471d-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="d471d-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="d471d-807">La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="d471d-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d471d-808">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d471d-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
