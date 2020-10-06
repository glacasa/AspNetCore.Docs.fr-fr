---
title: Hôte générique .NET
author: rick-anderson
description: Découvrez l’hôte générique .NET Core, qui est responsable de la gestion du démarrage et de la durée de vie des applications.
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
ms.openlocfilehash: 59cfae9ff619f8de894686c4b773d66e5cbe10ad
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754734"
---
# <a name="net-generic-host"></a><span data-ttu-id="d1fa3-103">Hôte générique .NET</span><span class="sxs-lookup"><span data-stu-id="d1fa3-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d1fa3-104">Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d1fa3-105">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-105">Host definition</span></span>

<span data-ttu-id="d1fa3-106">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d1fa3-107">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="d1fa3-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="d1fa3-108">Journalisation</span><span class="sxs-lookup"><span data-stu-id="d1fa3-108">Logging</span></span>
* <span data-ttu-id="d1fa3-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="d1fa3-109">Configuration</span></span>
* <span data-ttu-id="d1fa3-110">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-110">`IHostedService` implementations</span></span>

<span data-ttu-id="d1fa3-111">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d1fa3-112">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d1fa3-113">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d1fa3-114">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-114">Set up a host</span></span>

<span data-ttu-id="d1fa3-115">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d1fa3-116">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-116">The `Main` method:</span></span>

* <span data-ttu-id="d1fa3-117">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d1fa3-118">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d1fa3-119">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="d1fa3-120">Le code suivant crée une charge de travail non-HTTP avec une `IHostedService` implémentation ajoutée au conteneur di.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-120">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="d1fa3-121">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d1fa3-122">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d1fa3-123">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d1fa3-124">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d1fa3-125">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="d1fa3-125">Default builder settings</span></span>

<span data-ttu-id="d1fa3-126">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="d1fa3-127">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="d1fa3-128">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="d1fa3-129">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d1fa3-130">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d1fa3-130">Command-line arguments.</span></span>
* <span data-ttu-id="d1fa3-131">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="d1fa3-132">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="d1fa3-133">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="d1fa3-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d1fa3-134">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d1fa3-135">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-135">Environment variables.</span></span>
  * <span data-ttu-id="d1fa3-136">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d1fa3-136">Command-line arguments.</span></span>
* <span data-ttu-id="d1fa3-137">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d1fa3-138">Console</span><span class="sxs-lookup"><span data-stu-id="d1fa3-138">Console</span></span>
  * <span data-ttu-id="d1fa3-139">Débogage</span><span class="sxs-lookup"><span data-stu-id="d1fa3-139">Debug</span></span>
  * <span data-ttu-id="d1fa3-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="d1fa3-140">EventSource</span></span>
  * <span data-ttu-id="d1fa3-141">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="d1fa3-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d1fa3-142">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d1fa3-143">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d1fa3-144">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d1fa3-145">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d1fa3-146">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d1fa3-147">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d1fa3-148">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d1fa3-149">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-149">Enables IIS integration.</span></span> <span data-ttu-id="d1fa3-150">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d1fa3-151">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d1fa3-152">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="d1fa3-152">Framework-provided services</span></span>

<span data-ttu-id="d1fa3-153">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d1fa3-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d1fa3-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d1fa3-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d1fa3-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d1fa3-157">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d1fa3-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="d1fa3-159">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d1fa3-160">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d1fa3-161">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d1fa3-162">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d1fa3-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-163">IHostLifetime</span></span>

<span data-ttu-id="d1fa3-164">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d1fa3-165">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-165">The last implementation registered is used.</span></span>

<span data-ttu-id="d1fa3-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d1fa3-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d1fa3-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d1fa3-168">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="d1fa3-169">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d1fa3-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d1fa3-170">IHostEnvironment</span></span>

<span data-ttu-id="d1fa3-171">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d1fa3-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d1fa3-173">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d1fa3-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d1fa3-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d1fa3-175">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d1fa3-176">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-176">Host configuration</span></span>

<span data-ttu-id="d1fa3-177">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d1fa3-178">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="d1fa3-179">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d1fa3-180">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d1fa3-181">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d1fa3-182">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d1fa3-183">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d1fa3-184">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d1fa3-185">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d1fa3-186">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d1fa3-187">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d1fa3-188">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="d1fa3-188">App configuration</span></span>

<span data-ttu-id="d1fa3-189">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d1fa3-190">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d1fa3-191">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d1fa3-192">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d1fa3-193">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d1fa3-194">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d1fa3-195">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="d1fa3-195">Settings for all app types</span></span>

<span data-ttu-id="d1fa3-196">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d1fa3-197">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d1fa3-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-198">ApplicationName</span></span>

<span data-ttu-id="d1fa3-199">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d1fa3-200">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d1fa3-201">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-201">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-202">**Valeur par défaut**: nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d1fa3-203">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d1fa3-204">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d1fa3-205">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-205">ContentRoot</span></span>

<span data-ttu-id="d1fa3-206">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d1fa3-207">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d1fa3-208">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d1fa3-209">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-209">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-210">**Par défaut**: le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d1fa3-211">**Variable d’environnement**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d1fa3-212">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d1fa3-213">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-213">For more information, see:</span></span>

* [<span data-ttu-id="d1fa3-214">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="d1fa3-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d1fa3-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d1fa3-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-216">EnvironmentName</span></span>

<span data-ttu-id="d1fa3-217">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d1fa3-218">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d1fa3-219">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d1fa3-220">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-220">**Key**: `environment`</span></span>  
<span data-ttu-id="d1fa3-221">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-221">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-222">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-222">**Default**: `Production`</span></span>  
<span data-ttu-id="d1fa3-223">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d1fa3-224">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d1fa3-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d1fa3-225">ShutdownTimeout</span></span>

<span data-ttu-id="d1fa3-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d1fa3-227">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-227">The default value is five seconds.</span></span>  <span data-ttu-id="d1fa3-228">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="d1fa3-229">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d1fa3-230">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d1fa3-231">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d1fa3-232">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d1fa3-233">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d1fa3-234">**Clé** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d1fa3-235">**Type** : `int`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-235">**Type**: `int`</span></span>  
<span data-ttu-id="d1fa3-236">**Valeur par défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="d1fa3-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d1fa3-237">**Variable d’environnement**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d1fa3-238">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d1fa3-239">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="d1fa3-240">Désactiver le rechargement de la configuration d’application lors de la modification</span><span class="sxs-lookup"><span data-stu-id="d1fa3-240">Disable app configuration reload on change</span></span>

<span data-ttu-id="d1fa3-241">Par [défaut](xref:fundamentals/configuration/index#default), *appsettings.jssur* et *appSettings. { Environment}. JSON* est rechargé lorsque le fichier change.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-241">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="d1fa3-242">Pour désactiver ce comportement de rechargement dans ASP.NET Core 5,0 ou version ultérieure, affectez la valeur `hostBuilder:reloadConfigOnChange` à la clé `false` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-242">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="d1fa3-243">**Clé** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-243">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d1fa3-244">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-244">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-245">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-245">**Default**: `true`</span></span>  
<span data-ttu-id="d1fa3-246">**Argument de ligne de commande**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-246">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d1fa3-247">**Variable d’environnement**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-247">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="d1fa3-248">Le séparateur deux `:` -points () ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-248">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="d1fa3-249">Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-249">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="d1fa3-250">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="d1fa3-250">Settings for web apps</span></span>

<span data-ttu-id="d1fa3-251">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-251">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d1fa3-252">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-252">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d1fa3-253">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-253">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d1fa3-254">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-254">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d1fa3-255">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d1fa3-255">CaptureStartupErrors</span></span>

<span data-ttu-id="d1fa3-256">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-256">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d1fa3-257">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-257">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d1fa3-258">**Clé** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-258">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d1fa3-259">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-259">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-260">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-260">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d1fa3-261">**Variable d’environnement**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-261">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d1fa3-262">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-262">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d1fa3-263">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d1fa3-263">DetailedErrors</span></span>

<span data-ttu-id="d1fa3-264">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-264">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d1fa3-265">**Clé** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-265">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d1fa3-266">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-266">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-267">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-267">**Default**: `false`</span></span>  
<span data-ttu-id="d1fa3-268">**Variable d’environnement**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-268">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d1fa3-269">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d1fa3-270">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d1fa3-270">HostingStartupAssemblies</span></span>

<span data-ttu-id="d1fa3-271">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-271">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d1fa3-272">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-272">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d1fa3-273">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-273">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d1fa3-274">**Clé** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-274">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d1fa3-275">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-275">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-276">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d1fa3-276">**Default**: Empty string</span></span>  
<span data-ttu-id="d1fa3-277">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-277">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d1fa3-278">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-278">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d1fa3-279">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d1fa3-279">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d1fa3-280">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-280">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d1fa3-281">**Clé** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-281">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d1fa3-282">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-282">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-283">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d1fa3-283">**Default**: Empty string</span></span>  
<span data-ttu-id="d1fa3-284">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-284">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d1fa3-285">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d1fa3-286">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d1fa3-286">HTTPS_Port</span></span>

<span data-ttu-id="d1fa3-287">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-287">The HTTPS redirect port.</span></span> <span data-ttu-id="d1fa3-288">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-288">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d1fa3-289">**Clé** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-289">**Key**: `https_port`</span></span>  
<span data-ttu-id="d1fa3-290">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-290">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-291">Valeur **par défaut**: aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-291">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d1fa3-292">**Variable d’environnement**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-292">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d1fa3-293">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-293">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d1fa3-294">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d1fa3-294">PreferHostingUrls</span></span>

<span data-ttu-id="d1fa3-295">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-295">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d1fa3-296">**Clé** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-296">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d1fa3-297">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-298">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-298">**Default**: `true`</span></span>  
<span data-ttu-id="d1fa3-299">**Variable d’environnement**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-299">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d1fa3-300">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-300">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d1fa3-301">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d1fa3-301">PreventHostingStartup</span></span>

<span data-ttu-id="d1fa3-302">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-302">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d1fa3-303">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-303">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d1fa3-304">**Clé** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-304">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d1fa3-305">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-305">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-306">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-306">**Default**: `false`</span></span>  
<span data-ttu-id="d1fa3-307">**Variable d’environnement**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-307">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d1fa3-308">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-308">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d1fa3-309">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d1fa3-309">StartupAssembly</span></span>

<span data-ttu-id="d1fa3-310">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-310">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d1fa3-311">**Clé** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-311">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d1fa3-312">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-312">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-313">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="d1fa3-313">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d1fa3-314">**Variable d’environnement**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-314">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d1fa3-315">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-315">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d1fa3-316">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-316">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d1fa3-317">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-317">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d1fa3-318">URLs</span><span class="sxs-lookup"><span data-stu-id="d1fa3-318">URLs</span></span>

<span data-ttu-id="d1fa3-319">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-319">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d1fa3-320">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-320">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d1fa3-321">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-321">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d1fa3-322">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-322">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d1fa3-323">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-323">Supported formats vary among servers.</span></span>

<span data-ttu-id="d1fa3-324">**Clé** : `urls`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-324">**Key**: `urls`</span></span>  
<span data-ttu-id="d1fa3-325">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-325">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-326">**Par défaut**: `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-326">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d1fa3-327">**Variable d’environnement**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-327">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d1fa3-328">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-328">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d1fa3-329">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-329">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d1fa3-330">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-330">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d1fa3-331">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-331">WebRoot</span></span>

<span data-ttu-id="d1fa3-332">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-332">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d1fa3-333">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-333">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d1fa3-334">**Clé** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="d1fa3-335">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-335">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-336">**Valeur par défaut**: la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d1fa3-337">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-337">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d1fa3-338">**Variable d’environnement**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-338">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d1fa3-339">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-339">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d1fa3-340">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-340">For more information, see:</span></span>

* [<span data-ttu-id="d1fa3-341">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="d1fa3-341">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d1fa3-342">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-342">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d1fa3-343">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-343">Manage the host lifetime</span></span>

<span data-ttu-id="d1fa3-344">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-344">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d1fa3-345">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-345">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d1fa3-346">Exécuter</span><span class="sxs-lookup"><span data-stu-id="d1fa3-346">Run</span></span>

<span data-ttu-id="d1fa3-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d1fa3-348">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-348">RunAsync</span></span>

<span data-ttu-id="d1fa3-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d1fa3-350">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-350">RunConsoleAsync</span></span>

<span data-ttu-id="d1fa3-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-351"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d1fa3-352">Démarrer</span><span class="sxs-lookup"><span data-stu-id="d1fa3-352">Start</span></span>

<span data-ttu-id="d1fa3-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d1fa3-354">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-354">StartAsync</span></span>

<span data-ttu-id="d1fa3-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-355"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d1fa3-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-356"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d1fa3-357">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-357">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d1fa3-358">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-358">StopAsync</span></span>

<span data-ttu-id="d1fa3-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-359"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d1fa3-360">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d1fa3-360">WaitForShutdown</span></span>

<span data-ttu-id="d1fa3-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-361"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d1fa3-362">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-362">WaitForShutdownAsync</span></span>

<span data-ttu-id="d1fa3-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d1fa3-364">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="d1fa3-364">External control</span></span>

<span data-ttu-id="d1fa3-365">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-365">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="d1fa3-366">Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-366">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d1fa3-367">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-367">Host definition</span></span>

<span data-ttu-id="d1fa3-368">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-368">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d1fa3-369">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="d1fa3-369">Dependency injection (DI)</span></span>
* <span data-ttu-id="d1fa3-370">Journalisation</span><span class="sxs-lookup"><span data-stu-id="d1fa3-370">Logging</span></span>
* <span data-ttu-id="d1fa3-371">Configuration</span><span class="sxs-lookup"><span data-stu-id="d1fa3-371">Configuration</span></span>
* <span data-ttu-id="d1fa3-372">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-372">`IHostedService` implementations</span></span>

<span data-ttu-id="d1fa3-373">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-373">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d1fa3-374">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-374">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d1fa3-375">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-375">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d1fa3-376">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-376">Set up a host</span></span>

<span data-ttu-id="d1fa3-377">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-377">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d1fa3-378">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-378">The `Main` method:</span></span>

* <span data-ttu-id="d1fa3-379">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-379">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d1fa3-380">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-380">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d1fa3-381">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte générique :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-381">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="d1fa3-382">Le code suivant crée un hôte générique à l’aide d’une charge de travail non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-382">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="d1fa3-383">L' `IHostedService` implémentation est ajoutée au conteneur di :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-383">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="d1fa3-384">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-384">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d1fa3-385">Le code précédent est généré par les modèles ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-385">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="d1fa3-386">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d1fa3-387">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d1fa3-388">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d1fa3-389">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="d1fa3-389">Default builder settings</span></span>

<span data-ttu-id="d1fa3-390">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="d1fa3-391">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="d1fa3-392">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="d1fa3-393">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d1fa3-394">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d1fa3-394">Command-line arguments.</span></span>
* <span data-ttu-id="d1fa3-395">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="d1fa3-396">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="d1fa3-397">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="d1fa3-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d1fa3-398">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d1fa3-399">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-399">Environment variables.</span></span>
  * <span data-ttu-id="d1fa3-400">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="d1fa3-400">Command-line arguments.</span></span>
* <span data-ttu-id="d1fa3-401">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d1fa3-402">Console</span><span class="sxs-lookup"><span data-stu-id="d1fa3-402">Console</span></span>
  * <span data-ttu-id="d1fa3-403">Débogage</span><span class="sxs-lookup"><span data-stu-id="d1fa3-403">Debug</span></span>
  * <span data-ttu-id="d1fa3-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="d1fa3-404">EventSource</span></span>
  * <span data-ttu-id="d1fa3-405">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="d1fa3-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d1fa3-406">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d1fa3-407">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d1fa3-408">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d1fa3-409">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d1fa3-410">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d1fa3-411">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d1fa3-412">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d1fa3-413">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-413">Enables IIS integration.</span></span> <span data-ttu-id="d1fa3-414">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d1fa3-415">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d1fa3-416">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="d1fa3-416">Framework-provided services</span></span>

<span data-ttu-id="d1fa3-417">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d1fa3-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d1fa3-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d1fa3-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d1fa3-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d1fa3-421">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d1fa3-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="d1fa3-423">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d1fa3-424">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d1fa3-425">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d1fa3-426">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d1fa3-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-427">IHostLifetime</span></span>

<span data-ttu-id="d1fa3-428">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d1fa3-429">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-429">The last implementation registered is used.</span></span>

<span data-ttu-id="d1fa3-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d1fa3-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d1fa3-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d1fa3-432">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="d1fa3-433">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d1fa3-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d1fa3-434">IHostEnvironment</span></span>

<span data-ttu-id="d1fa3-435">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d1fa3-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d1fa3-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d1fa3-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d1fa3-438">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d1fa3-439">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d1fa3-440">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-440">Host configuration</span></span>

<span data-ttu-id="d1fa3-441">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d1fa3-442">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="d1fa3-443">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d1fa3-444">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="d1fa3-445">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d1fa3-446">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d1fa3-447">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d1fa3-448">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d1fa3-449">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d1fa3-450">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d1fa3-451">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d1fa3-452">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="d1fa3-452">App configuration</span></span>

<span data-ttu-id="d1fa3-453">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d1fa3-454">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d1fa3-455">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d1fa3-456">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d1fa3-457">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d1fa3-458">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d1fa3-459">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="d1fa3-459">Settings for all app types</span></span>

<span data-ttu-id="d1fa3-460">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d1fa3-461">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d1fa3-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-462">ApplicationName</span></span>

<span data-ttu-id="d1fa3-463">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d1fa3-464">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d1fa3-465">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-465">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-466">**Valeur par défaut**: nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d1fa3-467">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d1fa3-468">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-468">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d1fa3-469">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-469">ContentRoot</span></span>

<span data-ttu-id="d1fa3-470">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d1fa3-471">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d1fa3-472">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d1fa3-473">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-473">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-474">**Par défaut**: le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d1fa3-475">**Variable d’environnement**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d1fa3-476">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d1fa3-477">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-477">For more information, see:</span></span>

* [<span data-ttu-id="d1fa3-478">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="d1fa3-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d1fa3-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d1fa3-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d1fa3-480">EnvironmentName</span></span>

<span data-ttu-id="d1fa3-481">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d1fa3-482">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d1fa3-483">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d1fa3-484">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-484">**Key**: `environment`</span></span>  
<span data-ttu-id="d1fa3-485">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-485">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-486">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-486">**Default**: `Production`</span></span>  
<span data-ttu-id="d1fa3-487">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d1fa3-488">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d1fa3-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d1fa3-489">ShutdownTimeout</span></span>

<span data-ttu-id="d1fa3-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d1fa3-491">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-491">The default value is five seconds.</span></span>  <span data-ttu-id="d1fa3-492">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="d1fa3-493">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d1fa3-494">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d1fa3-495">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d1fa3-496">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d1fa3-497">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d1fa3-498">**Clé** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d1fa3-499">**Type** : `int`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-499">**Type**: `int`</span></span>  
<span data-ttu-id="d1fa3-500">**Valeur par défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="d1fa3-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d1fa3-501">**Variable d’environnement**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d1fa3-502">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d1fa3-503">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="d1fa3-504">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="d1fa3-504">Settings for web apps</span></span>

<span data-ttu-id="d1fa3-505">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d1fa3-506">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d1fa3-507">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d1fa3-508">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d1fa3-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d1fa3-509">CaptureStartupErrors</span></span>

<span data-ttu-id="d1fa3-510">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d1fa3-511">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d1fa3-512">**Clé** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d1fa3-513">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-514">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d1fa3-515">**Variable d’environnement**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d1fa3-516">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d1fa3-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d1fa3-517">DetailedErrors</span></span>

<span data-ttu-id="d1fa3-518">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d1fa3-519">**Clé** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d1fa3-520">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-521">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-521">**Default**: `false`</span></span>  
<span data-ttu-id="d1fa3-522">**Variable d’environnement**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d1fa3-523">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d1fa3-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d1fa3-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="d1fa3-525">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d1fa3-526">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d1fa3-527">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d1fa3-528">**Clé** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d1fa3-529">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-529">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-530">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d1fa3-530">**Default**: Empty string</span></span>  
<span data-ttu-id="d1fa3-531">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d1fa3-532">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d1fa3-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d1fa3-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d1fa3-534">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d1fa3-535">**Clé** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d1fa3-536">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-536">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-537">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="d1fa3-537">**Default**: Empty string</span></span>  
<span data-ttu-id="d1fa3-538">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d1fa3-539">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d1fa3-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d1fa3-540">HTTPS_Port</span></span>

<span data-ttu-id="d1fa3-541">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-541">The HTTPS redirect port.</span></span> <span data-ttu-id="d1fa3-542">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d1fa3-543">**Clé** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="d1fa3-544">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-544">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-545">Valeur **par défaut**: aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d1fa3-546">**Variable d’environnement**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d1fa3-547">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d1fa3-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d1fa3-548">PreferHostingUrls</span></span>

<span data-ttu-id="d1fa3-549">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d1fa3-550">**Clé** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d1fa3-551">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-552">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-552">**Default**: `true`</span></span>  
<span data-ttu-id="d1fa3-553">**Variable d’environnement**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d1fa3-554">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d1fa3-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d1fa3-555">PreventHostingStartup</span></span>

<span data-ttu-id="d1fa3-556">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d1fa3-557">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d1fa3-558">**Clé** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d1fa3-559">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d1fa3-560">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-560">**Default**: `false`</span></span>  
<span data-ttu-id="d1fa3-561">**Variable d’environnement**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d1fa3-562">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d1fa3-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d1fa3-563">StartupAssembly</span></span>

<span data-ttu-id="d1fa3-564">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d1fa3-565">**Clé** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d1fa3-566">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-566">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-567">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="d1fa3-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d1fa3-568">**Variable d’environnement**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d1fa3-569">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d1fa3-570">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d1fa3-571">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d1fa3-572">URLs</span><span class="sxs-lookup"><span data-stu-id="d1fa3-572">URLs</span></span>

<span data-ttu-id="d1fa3-573">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d1fa3-574">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d1fa3-575">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d1fa3-576">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d1fa3-577">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="d1fa3-578">**Clé** : `urls`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-578">**Key**: `urls`</span></span>  
<span data-ttu-id="d1fa3-579">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-579">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-580">**Par défaut**: `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d1fa3-581">**Variable d’environnement**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d1fa3-582">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d1fa3-583">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d1fa3-584">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d1fa3-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-585">WebRoot</span></span>

<span data-ttu-id="d1fa3-586">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-586">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d1fa3-587">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-587">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d1fa3-588">**Clé** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-588">**Key**: `webroot`</span></span>  
<span data-ttu-id="d1fa3-589">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-589">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-590">**Valeur par défaut**: la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="d1fa3-590">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d1fa3-591">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-591">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d1fa3-592">**Variable d’environnement**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d1fa3-593">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-593">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d1fa3-594">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-594">For more information, see:</span></span>

* [<span data-ttu-id="d1fa3-595">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="d1fa3-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d1fa3-596">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d1fa3-596">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d1fa3-597">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-597">Manage the host lifetime</span></span>

<span data-ttu-id="d1fa3-598">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d1fa3-599">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d1fa3-600">Exécuter</span><span class="sxs-lookup"><span data-stu-id="d1fa3-600">Run</span></span>

<span data-ttu-id="d1fa3-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d1fa3-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-602">RunAsync</span></span>

<span data-ttu-id="d1fa3-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d1fa3-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-604">RunConsoleAsync</span></span>

<span data-ttu-id="d1fa3-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d1fa3-606">Démarrer</span><span class="sxs-lookup"><span data-stu-id="d1fa3-606">Start</span></span>

<span data-ttu-id="d1fa3-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d1fa3-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-608">StartAsync</span></span>

<span data-ttu-id="d1fa3-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d1fa3-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d1fa3-611">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d1fa3-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-612">StopAsync</span></span>

<span data-ttu-id="d1fa3-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d1fa3-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d1fa3-614">WaitForShutdown</span></span>

<span data-ttu-id="d1fa3-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d1fa3-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="d1fa3-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d1fa3-618">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="d1fa3-618">External control</span></span>

<span data-ttu-id="d1fa3-619">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="d1fa3-620">Les applications ASP.NET Core configurent et lancent un hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d1fa3-621">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d1fa3-622">Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="d1fa3-623">L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d1fa3-624">La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d1fa3-625">L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="d1fa3-626">Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d1fa3-627">L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="d1fa3-628">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1fa3-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1fa3-629">Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré*.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="d1fa3-630">N’exécutez pas l’exemple dans une `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="d1fa3-631">Pour définir la console dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="d1fa3-632">Ouvrez le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="d1fa3-633">Dans la configuration **.NET Core Launch (console)**, recherchez l’entrée **console**.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="d1fa3-634">Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="d1fa3-635">Introduction</span><span class="sxs-lookup"><span data-stu-id="d1fa3-635">Introduction</span></span>

<span data-ttu-id="d1fa3-636">La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="d1fa3-637">Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="d1fa3-638"><xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="d1fa3-639">Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="d1fa3-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d1fa3-641">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-641">Set up a host</span></span>

<span data-ttu-id="d1fa3-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="d1fa3-643">Options</span><span class="sxs-lookup"><span data-stu-id="d1fa3-643">Options</span></span>

<span data-ttu-id="d1fa3-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d1fa3-645">Délai d’expiration</span><span class="sxs-lookup"><span data-stu-id="d1fa3-645">Shutdown timeout</span></span>

<span data-ttu-id="d1fa3-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d1fa3-647">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-647">The default value is five seconds.</span></span>

<span data-ttu-id="d1fa3-648">La configuration d’option suivante dans `Program.Main` augmente le délai d’attente d’arrêt de cinq secondes par défaut à 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="d1fa3-649">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="d1fa3-649">Default services</span></span>

<span data-ttu-id="d1fa3-650">Les services suivants sont inscrits au moment de l’initialisation de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="d1fa3-651">[Environnement](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="d1fa3-652">[Configuration](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="d1fa3-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d1fa3-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="d1fa3-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d1fa3-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="d1fa3-655">[Options](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="d1fa3-656">[Journalisation](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="d1fa3-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d1fa3-657">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-657">Host configuration</span></span>

<span data-ttu-id="d1fa3-658">La création de la configuration d’hôte fait appel aux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-658">Host configuration is created by:</span></span>

* <span data-ttu-id="d1fa3-659">Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="d1fa3-660">Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="d1fa3-661">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="d1fa3-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d1fa3-662">Clé d’application (nom)</span><span class="sxs-lookup"><span data-stu-id="d1fa3-662">Application key (name)</span></span>

<span data-ttu-id="d1fa3-663">La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="d1fa3-664">Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="d1fa3-665">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d1fa3-666">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-666">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-667">**Par défaut** : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d1fa3-668">**Définir à l’aide**de : `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="d1fa3-669">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d1fa3-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="d1fa3-670">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="d1fa3-670">Content root</span></span>

<span data-ttu-id="d1fa3-671">Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="d1fa3-672">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d1fa3-673">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-673">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-674">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d1fa3-675">**Définir à l’aide**de : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="d1fa3-676">**Variable d’environnement**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d1fa3-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d1fa3-677">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="d1fa3-678">Pour plus d’informations, consultez [principes de base : racine du contenu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="d1fa3-679">Environnement</span><span class="sxs-lookup"><span data-stu-id="d1fa3-679">Environment</span></span>

<span data-ttu-id="d1fa3-680">Définit l' [environnement](xref:fundamentals/environments)de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d1fa3-681">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-681">**Key**: `environment`</span></span>  
<span data-ttu-id="d1fa3-682">**Type** : `string`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-682">**Type**: `string`</span></span>  
<span data-ttu-id="d1fa3-683">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-683">**Default**: `Production`</span></span>  
<span data-ttu-id="d1fa3-684">**Définir à l’aide**de : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d1fa3-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="d1fa3-685">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d1fa3-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d1fa3-686">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-686">The environment can be set to any value.</span></span> <span data-ttu-id="d1fa3-687">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d1fa3-688">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="d1fa3-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="d1fa3-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="d1fa3-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="d1fa3-691">La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="d1fa3-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d1fa3-693">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d1fa3-694">Aucun fournisseur n’est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-694">No providers are included by default.</span></span> <span data-ttu-id="d1fa3-695">Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="d1fa3-696">Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="d1fa3-697">Configuration de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-697">Environment variable configuration.</span></span>
* <span data-ttu-id="d1fa3-698">Configuration de l’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="d1fa3-699">Tout autre fournisseur de configuration obligatoire.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-699">Any other required configuration providers.</span></span>

<span data-ttu-id="d1fa3-700">Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="d1fa3-701">L’exemple d’application utilise un fichier JSON, *hostsettings.json*, et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="d1fa3-702">Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="d1fa3-703">`AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="d1fa3-704">L’exemple d’application utilise un préfixe `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="d1fa3-705">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d1fa3-706">Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d1fa3-707">Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="d1fa3-708">Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="d1fa3-709">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d1fa3-710">Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="d1fa3-711">La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="d1fa3-712">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-712">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="d1fa3-713">Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="d1fa3-714">Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="d1fa3-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d1fa3-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d1fa3-716">Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="d1fa3-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="d1fa3-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d1fa3-719">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="d1fa3-720">La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="d1fa3-721">La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="d1fa3-722">Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="d1fa3-723">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="d1fa3-723">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="d1fa3-724">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-724">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="d1fa3-725">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-725">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="d1fa3-726">Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="d1fa3-727">L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="d1fa3-728">Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="d1fa3-729">Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="d1fa3-730">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="d1fa3-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d1fa3-731">ConfigureServices</span></span>

<span data-ttu-id="d1fa3-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d1fa3-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d1fa3-734">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d1fa3-735">Pour plus d’informations, consultez <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="d1fa3-736">L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="d1fa3-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="d1fa3-737">ConfigureLogging</span></span>

<span data-ttu-id="d1fa3-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="d1fa3-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="d1fa3-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-740">UseConsoleLifetime</span></span>

<span data-ttu-id="d1fa3-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="d1fa3-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="d1fa3-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="d1fa3-744">La dernière durée de vie inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="d1fa3-745">Configuration du conteneur</span><span class="sxs-lookup"><span data-stu-id="d1fa3-745">Container configuration</span></span>

<span data-ttu-id="d1fa3-746">Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="d1fa3-747">L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="d1fa3-748">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="d1fa3-749">La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="d1fa3-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="d1fa3-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d1fa3-752">Créer un conteneur de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="d1fa3-753">Fournir une fabrique de conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="d1fa3-754">Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="d1fa3-755">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="d1fa3-755">Extensibility</span></span>

<span data-ttu-id="d1fa3-756">L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="d1fa3-757">L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="d1fa3-758">Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="d1fa3-759">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="d1fa3-759">Manage the host</span></span>

<span data-ttu-id="d1fa3-760">L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d1fa3-761">Exécuter</span><span class="sxs-lookup"><span data-stu-id="d1fa3-761">Run</span></span>

<span data-ttu-id="d1fa3-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="d1fa3-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-763">RunAsync</span></span>

<span data-ttu-id="d1fa3-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="d1fa3-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-765">RunConsoleAsync</span></span>

<span data-ttu-id="d1fa3-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="d1fa3-767">Start et StopAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-767">Start and StopAsync</span></span>

<span data-ttu-id="d1fa3-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="d1fa3-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="d1fa3-770">StartAsync et StopAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="d1fa3-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="d1fa3-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="d1fa3-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d1fa3-773">WaitForShutdown</span></span>

<span data-ttu-id="d1fa3-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché via le <xref:Microsoft.Extensions.Hosting.IHostLifetime> , par exemple `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (écoute la <kbd>touche Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d1fa3-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="d1fa3-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="d1fa3-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d1fa3-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="d1fa3-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="d1fa3-778">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="d1fa3-778">External control</span></span>

<span data-ttu-id="d1fa3-779">Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="d1fa3-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d1fa3-781">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d1fa3-782">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="d1fa3-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="d1fa3-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="d1fa3-784">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="d1fa3-785">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d1fa3-786">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d1fa3-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="d1fa3-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="d1fa3-788">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d1fa3-789">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="d1fa3-789">Cancellation Token</span></span> | <span data-ttu-id="d1fa3-790">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="d1fa3-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="d1fa3-791">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="d1fa3-792">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d1fa3-793">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-793">All requests should be processed.</span></span> <span data-ttu-id="d1fa3-794">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="d1fa3-795">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d1fa3-796">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-796">Requests may still be processing.</span></span> <span data-ttu-id="d1fa3-797">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="d1fa3-798">Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="d1fa3-799">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="d1fa3-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1fa3-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="d1fa3-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1fa3-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="d1fa3-802">La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="d1fa3-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="d1fa3-803">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d1fa3-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
