---
title: Hôte générique .NET
author: rick-anderson
description: Découvrez l’hôte générique .NET Core, qui est responsable de la gestion du démarrage et de la durée de vie des applications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: b528a33fa52bfe56faaf9f3ff8c7e43db0d4e184
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384035"
---
# <a name="net-generic-host"></a><span data-ttu-id="fbd89-103">Hôte générique .NET</span><span class="sxs-lookup"><span data-stu-id="fbd89-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="fbd89-104">Les modèles ASP.NET Core créent un hôte générique<xref:Microsoft.Extensions.Hosting.HostBuilder>de base .NET ( ).</span><span class="sxs-lookup"><span data-stu-id="fbd89-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="fbd89-105">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-105">Host definition</span></span>

<span data-ttu-id="fbd89-106">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="fbd89-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="fbd89-107">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="fbd89-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="fbd89-108">Journalisation</span><span class="sxs-lookup"><span data-stu-id="fbd89-108">Logging</span></span>
* <span data-ttu-id="fbd89-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="fbd89-109">Configuration</span></span>
* <span data-ttu-id="fbd89-110">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="fbd89-110">`IHostedService` implementations</span></span>

<span data-ttu-id="fbd89-111">Quand un hôte démarre, il appelle `IHostedService.StartAsync` sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> qu’il trouve dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fbd89-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="fbd89-112">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="fbd89-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="fbd89-113">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="fbd89-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="fbd89-114">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-114">Set up a host</span></span>

<span data-ttu-id="fbd89-115">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="fbd89-116">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-116">The `Main` method:</span></span>

* <span data-ttu-id="fbd89-117">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="fbd89-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="fbd89-118">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="fbd89-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="fbd89-119">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="fbd89-120">Le code suivant crée une charge `IHostedService` de travail non-HTTP avec une implémentation ajoutée au conteneur DI.</span><span class="sxs-lookup"><span data-stu-id="fbd89-120">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="fbd89-121">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="fbd89-122">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="fbd89-123">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="fbd89-124">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="fbd89-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="fbd89-125">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="fbd89-125">Default builder settings</span></span>

<span data-ttu-id="fbd89-126">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="fbd89-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="fbd89-127">Définit la [racine de](xref:fundamentals/index#content-root) contenu <xref:System.IO.Directory.GetCurrentDirectory*>sur le chemin retourné par .</span><span class="sxs-lookup"><span data-stu-id="fbd89-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="fbd89-128">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="fbd89-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="fbd89-129">Variables de l’environnement préfixées avec `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="fbd89-130">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="fbd89-130">Command-line arguments.</span></span>
* <span data-ttu-id="fbd89-131">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="fbd89-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="fbd89-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fbd89-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="fbd89-133">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="fbd89-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="fbd89-134">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="fbd89-135">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-135">Environment variables.</span></span>
  * <span data-ttu-id="fbd89-136">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="fbd89-136">Command-line arguments.</span></span>
* <span data-ttu-id="fbd89-137">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="fbd89-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="fbd89-138">Console</span><span class="sxs-lookup"><span data-stu-id="fbd89-138">Console</span></span>
  * <span data-ttu-id="fbd89-139">Débogage</span><span class="sxs-lookup"><span data-stu-id="fbd89-139">Debug</span></span>
  * <span data-ttu-id="fbd89-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="fbd89-140">EventSource</span></span>
  * <span data-ttu-id="fbd89-141">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="fbd89-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="fbd89-142">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="fbd89-143">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="fbd89-144">Charge la configuration de l’hôte `ASPNETCORE_`à partir de variables de l’environnement préfixées avec .</span><span class="sxs-lookup"><span data-stu-id="fbd89-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="fbd89-145">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="fbd89-146">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="fbd89-147">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fbd89-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="fbd89-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="fbd89-149">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="fbd89-149">Enables IIS integration.</span></span> <span data-ttu-id="fbd89-150">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="fbd89-151">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fbd89-152">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="fbd89-152">Framework-provided services</span></span>

<span data-ttu-id="fbd89-153">Les services suivants sont enregistrés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="fbd89-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="fbd89-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="fbd89-155">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="fbd89-156">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fbd89-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="fbd89-157">Pour plus d’informations sur <xref:fundamentals/dependency-injection#framework-provided-services>les services fournis par le cadre, voir .</span><span class="sxs-lookup"><span data-stu-id="fbd89-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="fbd89-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="fbd89-159">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="fbd89-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="fbd89-160">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="fbd89-161">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="fbd89-162">L’exemple suivant `IHostedService` est une `IHostApplicationLifetime` implémentation qui enregistre les événements :</span><span class="sxs-lookup"><span data-stu-id="fbd89-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="fbd89-163">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-163">IHostLifetime</span></span>

<span data-ttu-id="fbd89-164">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fbd89-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="fbd89-165">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-165">The last implementation registered is used.</span></span>

<span data-ttu-id="fbd89-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="fbd89-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="fbd89-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="fbd89-168">Écoutez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="fbd89-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="fbd89-169">Débloque des extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="fbd89-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="fbd89-170">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fbd89-170">IHostEnvironment</span></span>

<span data-ttu-id="fbd89-171">Injecter <xref:Microsoft.Extensions.Hosting.IHostEnvironment> le service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="fbd89-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="fbd89-172">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fbd89-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="fbd89-173">EnvironmentName (en)</span><span class="sxs-lookup"><span data-stu-id="fbd89-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="fbd89-174">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="fbd89-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="fbd89-175">Les applications `IWebHostEnvironment` Web implémentent l’interface, qui hérite `IHostEnvironment` et ajoute le [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="fbd89-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="fbd89-176">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-176">Host configuration</span></span>

<span data-ttu-id="fbd89-177">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="fbd89-178">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="fbd89-179">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="fbd89-180">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fbd89-181">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fbd89-182">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="fbd89-183">Le fournisseur variable d’environnement avec des arguments de préfixe `DOTNET_` et de commande-ligne sont inclus par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="fbd89-184">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="fbd89-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="fbd89-185">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="fbd89-186">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="fbd89-187">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="fbd89-188">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="fbd89-188">App configuration</span></span>

<span data-ttu-id="fbd89-189">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fbd89-190">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fbd89-191">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="fbd89-192">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fbd89-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="fbd89-193">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="fbd89-194">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="fbd89-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="fbd89-195">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="fbd89-195">Settings for all app types</span></span>

<span data-ttu-id="fbd89-196">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbd89-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="fbd89-197">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="fbd89-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fbd89-198">ApplicationName</span></span>

<span data-ttu-id="fbd89-199">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="fbd89-200">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="fbd89-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="fbd89-201">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-201">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-202">**Défaut**: Nom de l’assemblage qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="fbd89-203">**Variable environnement**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="fbd89-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="fbd89-204">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="fbd89-205">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-205">ContentRoot</span></span>

<span data-ttu-id="fbd89-206">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="fbd89-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="fbd89-207">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="fbd89-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="fbd89-208">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="fbd89-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="fbd89-209">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-209">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-210">**Défaut**: Le dossier où se trouve l’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="fbd89-211">**Variable environnement**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="fbd89-212">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="fbd89-213">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-213">For more information, see:</span></span>

* [<span data-ttu-id="fbd89-214">Fondamentaux : Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="fbd89-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="fbd89-215">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="fbd89-216">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="fbd89-216">EnvironmentName</span></span>

<span data-ttu-id="fbd89-217">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="fbd89-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="fbd89-218">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="fbd89-219">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="fbd89-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="fbd89-220">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="fbd89-220">**Key**: `environment`</span></span>  
<span data-ttu-id="fbd89-221">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-221">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-222">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="fbd89-222">**Default**: `Production`</span></span>  
<span data-ttu-id="fbd89-223">**Variable environnement**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="fbd89-224">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="fbd89-225">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="fbd89-225">ShutdownTimeout</span></span>

<span data-ttu-id="fbd89-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="fbd89-227">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="fbd89-227">The default value is five seconds.</span></span>  <span data-ttu-id="fbd89-228">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="fbd89-229">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="fbd89-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="fbd89-230">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="fbd89-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="fbd89-231">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fbd89-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="fbd89-232">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="fbd89-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="fbd89-233">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fbd89-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="fbd89-234">**Clé**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="fbd89-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="fbd89-235">**Type**:`int`</span><span class="sxs-lookup"><span data-stu-id="fbd89-235">**Type**: `int`</span></span>  
<span data-ttu-id="fbd89-236">**Défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="fbd89-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="fbd89-237">**Variable environnement**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="fbd89-238">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="fbd89-239">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="fbd89-240">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="fbd89-240">Settings for web apps</span></span>

<span data-ttu-id="fbd89-241">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbd89-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="fbd89-242">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="fbd89-243">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="fbd89-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="fbd89-244">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fbd89-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="fbd89-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="fbd89-245">CaptureStartupErrors</span></span>

<span data-ttu-id="fbd89-246">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="fbd89-247">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="fbd89-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="fbd89-248">**Clé**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="fbd89-248">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="fbd89-249">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-249">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-250">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="fbd89-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="fbd89-251">**Variable environnement**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="fbd89-252">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="fbd89-253">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="fbd89-253">DetailedErrors</span></span>

<span data-ttu-id="fbd89-254">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="fbd89-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="fbd89-255">**Clé**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="fbd89-255">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="fbd89-256">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-256">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-257">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="fbd89-257">**Default**: `false`</span></span>  
<span data-ttu-id="fbd89-258">**Variable environnement**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="fbd89-259">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="fbd89-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="fbd89-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="fbd89-261">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="fbd89-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="fbd89-262">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="fbd89-263">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="fbd89-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="fbd89-264">**Clé**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fbd89-264">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="fbd89-265">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-265">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-266">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="fbd89-266">**Default**: Empty string</span></span>  
<span data-ttu-id="fbd89-267">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fbd89-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="fbd89-268">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="fbd89-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="fbd89-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="fbd89-270">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="fbd89-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="fbd89-271">**Clé**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fbd89-271">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="fbd89-272">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-272">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-273">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="fbd89-273">**Default**: Empty string</span></span>  
<span data-ttu-id="fbd89-274">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fbd89-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="fbd89-275">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="fbd89-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="fbd89-276">HTTPS_Port</span></span>

<span data-ttu-id="fbd89-277">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fbd89-277">The HTTPS redirect port.</span></span> <span data-ttu-id="fbd89-278">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="fbd89-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fbd89-279">**Clé**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="fbd89-279">**Key**: `https_port`</span></span>  
<span data-ttu-id="fbd89-280">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-280">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-281">**Défaut**: Une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="fbd89-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="fbd89-282">**Variable environnement**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="fbd89-283">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="fbd89-284">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="fbd89-284">PreferHostingUrls</span></span>

<span data-ttu-id="fbd89-285">Indique si l’hôte doit écouter sur les `IWebHostBuilder` URL configurées avec les `IServer` URL configurées au lieu de ces URL configurées avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="fbd89-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="fbd89-286">**Clé**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="fbd89-286">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="fbd89-287">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-287">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-288">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="fbd89-288">**Default**: `true`</span></span>  
<span data-ttu-id="fbd89-289">**Variable environnement**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="fbd89-290">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="fbd89-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="fbd89-291">PreventHostingStartup</span></span>

<span data-ttu-id="fbd89-292">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="fbd89-293">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="fbd89-294">**Clé**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="fbd89-294">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="fbd89-295">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-295">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-296">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="fbd89-296">**Default**: `false`</span></span>  
<span data-ttu-id="fbd89-297">**Variable environnement**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="fbd89-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="fbd89-298">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="fbd89-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="fbd89-299">StartupAssembly</span></span>

<span data-ttu-id="fbd89-300">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="fbd89-301">**Clé**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="fbd89-301">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="fbd89-302">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-302">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-303">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="fbd89-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="fbd89-304">**Variable environnement**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="fbd89-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="fbd89-305">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="fbd89-306">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="fbd89-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="fbd89-307">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="fbd89-308">URLs</span><span class="sxs-lookup"><span data-stu-id="fbd89-308">URLs</span></span>

<span data-ttu-id="fbd89-309">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="fbd89-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="fbd89-310">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="fbd89-311">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="fbd89-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="fbd89-312">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="fbd89-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="fbd89-313">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="fbd89-314">**Clé**:`urls`</span><span class="sxs-lookup"><span data-stu-id="fbd89-314">**Key**: `urls`</span></span>  
<span data-ttu-id="fbd89-315">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-315">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-316">**Défaut** `http://localhost:5000` : et`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="fbd89-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="fbd89-317">**Variable environnement**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="fbd89-318">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="fbd89-319">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="fbd89-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="fbd89-320">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="fbd89-321">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-321">WebRoot</span></span>

<span data-ttu-id="fbd89-322">La propriété [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin relatif vers les actifs statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-322">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="fbd89-323">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="fbd89-323">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="fbd89-324">**Clé**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="fbd89-324">**Key**: `webroot`</span></span>  
<span data-ttu-id="fbd89-325">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-325">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-326">**Défaut**: La `wwwroot`valeur par défaut est .</span><span class="sxs-lookup"><span data-stu-id="fbd89-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="fbd89-327">Le chemin vers *la « racine de contenu »/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="fbd89-327">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="fbd89-328">**Variable environnement**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="fbd89-329">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-329">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="fbd89-330">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-330">For more information, see:</span></span>

* [<span data-ttu-id="fbd89-331">Fondamentaux : racine web</span><span class="sxs-lookup"><span data-stu-id="fbd89-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="fbd89-332">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-332">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="fbd89-333">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-333">Manage the host lifetime</span></span>

<span data-ttu-id="fbd89-334">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="fbd89-335">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fbd89-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="fbd89-336">Exécuter</span><span class="sxs-lookup"><span data-stu-id="fbd89-336">Run</span></span>

<span data-ttu-id="fbd89-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="fbd89-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-338">RunAsync</span></span>

<span data-ttu-id="fbd89-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="fbd89-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="fbd89-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-340">RunConsoleAsync</span></span>

<span data-ttu-id="fbd89-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fbd89-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="fbd89-342">Démarrer</span><span class="sxs-lookup"><span data-stu-id="fbd89-342">Start</span></span>

<span data-ttu-id="fbd89-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="fbd89-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="fbd89-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-344">StartAsync</span></span>

<span data-ttu-id="fbd89-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="fbd89-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="fbd89-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="fbd89-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="fbd89-347">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="fbd89-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="fbd89-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-348">StopAsync</span></span>

<span data-ttu-id="fbd89-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="fbd89-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="fbd89-350">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="fbd89-350">WaitForShutdown</span></span>

<span data-ttu-id="fbd89-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le fil d’appel jusqu’à ce que l’arrêt soit déclenché par l’IHostLifetime, par exemple via <kbd>Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="fbd89-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="fbd89-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="fbd89-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="fbd89-354">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="fbd89-354">External control</span></span>

<span data-ttu-id="fbd89-355">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="fbd89-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="fbd89-356">Les applications ASP.NET Core configurent et lancent un hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="fbd89-357">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="fbd89-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="fbd89-358">Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbd89-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="fbd89-359">L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="fbd89-360">La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="fbd89-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="fbd89-361">L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web.</span><span class="sxs-lookup"><span data-stu-id="fbd89-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="fbd89-362">Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="fbd89-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="fbd89-363">L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbd89-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="fbd89-364">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fbd89-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fbd89-365">Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré*.</span><span class="sxs-lookup"><span data-stu-id="fbd89-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="fbd89-366">N’exécutez pas l’exemple dans une `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="fbd89-367">Pour définir la console dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="fbd89-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="fbd89-368">Ouvrez le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="fbd89-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="fbd89-369">Dans la configuration **.NET Core Launch (console)**, recherchez l’entrée **console**.</span><span class="sxs-lookup"><span data-stu-id="fbd89-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="fbd89-370">Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="fbd89-371">Introduction</span><span class="sxs-lookup"><span data-stu-id="fbd89-371">Introduction</span></span>

<span data-ttu-id="fbd89-372">La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="fbd89-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="fbd89-373">Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).</span><span class="sxs-lookup"><span data-stu-id="fbd89-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="fbd89-374"><xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code.</span><span class="sxs-lookup"><span data-stu-id="fbd89-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="fbd89-375">Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="fbd89-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="fbd89-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="fbd89-377">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-377">Set up a host</span></span>

<span data-ttu-id="fbd89-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="fbd89-379">Options</span><span class="sxs-lookup"><span data-stu-id="fbd89-379">Options</span></span>

<span data-ttu-id="fbd89-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="fbd89-381">Délai d’expiration</span><span class="sxs-lookup"><span data-stu-id="fbd89-381">Shutdown timeout</span></span>

<span data-ttu-id="fbd89-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="fbd89-383">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="fbd89-383">The default value is five seconds.</span></span>

<span data-ttu-id="fbd89-384">La configuration d’option suivante augmente `Program.Main` le délai d’arrêt par défaut de cinq secondes à 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-384">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="fbd89-385">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="fbd89-385">Default services</span></span>

<span data-ttu-id="fbd89-386">Les services suivants sont inscrits au moment de l’initialisation de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="fbd89-387">[Environnement](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="fbd89-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="fbd89-388">[Configuration](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="fbd89-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="fbd89-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="fbd89-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="fbd89-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="fbd89-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="fbd89-391">[Options](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="fbd89-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="fbd89-392">[Enregistrement](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="fbd89-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="fbd89-393">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-393">Host configuration</span></span>

<span data-ttu-id="fbd89-394">La création de la configuration d’hôte fait appel aux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-394">Host configuration is created by:</span></span>

* <span data-ttu-id="fbd89-395">Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="fbd89-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="fbd89-396">Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="fbd89-397">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="fbd89-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="fbd89-398">Clé d’application (nom)</span><span class="sxs-lookup"><span data-stu-id="fbd89-398">Application key (name)</span></span>

<span data-ttu-id="fbd89-399">La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="fbd89-400">Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :</span><span class="sxs-lookup"><span data-stu-id="fbd89-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="fbd89-401">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="fbd89-401">**Key**: `applicationName`</span></span>  
<span data-ttu-id="fbd89-402">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-402">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-403">**Par défaut** : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="fbd89-404">**Ensemble à l’aide**de :`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="fbd89-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="fbd89-405">**Variable de l’environnement**: `<PREFIX_>APPLICATIONNAME` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="fbd89-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="fbd89-406">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="fbd89-406">Content root</span></span>

<span data-ttu-id="fbd89-407">Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="fbd89-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="fbd89-408">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="fbd89-408">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="fbd89-409">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-409">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-410">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="fbd89-411">**Ensemble à l’aide**de :`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="fbd89-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="fbd89-412">**Variable de l’environnement**: `<PREFIX_>CONTENTROOT` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="fbd89-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="fbd89-413">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="fbd89-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="fbd89-414">Pour plus d’informations, voir [Fundamentals: Content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="fbd89-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="fbd89-415">Environnement</span><span class="sxs-lookup"><span data-stu-id="fbd89-415">Environment</span></span>

<span data-ttu-id="fbd89-416">Définit [l’environnement](xref:fundamentals/environments)de l’application .</span><span class="sxs-lookup"><span data-stu-id="fbd89-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="fbd89-417">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="fbd89-417">**Key**: `environment`</span></span>  
<span data-ttu-id="fbd89-418">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-418">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-419">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="fbd89-419">**Default**: `Production`</span></span>  
<span data-ttu-id="fbd89-420">**Ensemble à l’aide**de :`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="fbd89-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="fbd89-421">**Variable de l’environnement**: `<PREFIX_>ENVIRONMENT` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="fbd89-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="fbd89-422">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="fbd89-422">The environment can be set to any value.</span></span> <span data-ttu-id="fbd89-423">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="fbd89-424">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="fbd89-424">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="fbd89-425">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="fbd89-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="fbd89-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="fbd89-427">La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="fbd89-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="fbd89-429">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="fbd89-430">Aucun fournisseur n’est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-430">No providers are included by default.</span></span> <span data-ttu-id="fbd89-431">Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :</span><span class="sxs-lookup"><span data-stu-id="fbd89-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="fbd89-432">Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="fbd89-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="fbd89-433">Configuration de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-433">Environment variable configuration.</span></span>
* <span data-ttu-id="fbd89-434">Configuration de l’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="fbd89-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="fbd89-435">Tout autre fournisseur de configuration obligatoire.</span><span class="sxs-lookup"><span data-stu-id="fbd89-435">Any other required configuration providers.</span></span>

<span data-ttu-id="fbd89-436">Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="fbd89-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="fbd89-437">L’exemple d’application utilise un fichier JSON, *hostsettings.json*, et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.</span><span class="sxs-lookup"><span data-stu-id="fbd89-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="fbd89-438">Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="fbd89-439">`AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fbd89-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="fbd89-440">L’exemple d’application utilise un préfixe `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="fbd89-441">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="fbd89-442">Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="fbd89-443">Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fbd89-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="fbd89-444">Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="fbd89-445">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="fbd89-446">Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="fbd89-447">La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="fbd89-448">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="fbd89-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="fbd89-449">Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="fbd89-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="fbd89-450">Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="fbd89-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="fbd89-451">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="fbd89-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="fbd89-452">Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="fbd89-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="fbd89-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="fbd89-455">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="fbd89-456">La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="fbd89-457">La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="fbd89-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="fbd89-458">Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="fbd89-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="fbd89-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="fbd89-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="fbd89-460">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="fbd89-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="fbd89-461">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="fbd89-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="fbd89-462">Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="fbd89-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="fbd89-463">L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :</span><span class="sxs-lookup"><span data-stu-id="fbd89-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="fbd89-464">Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="fbd89-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="fbd89-465">Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="fbd89-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="fbd89-466">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="fbd89-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="fbd89-467">ConfigureServices</span></span>

<span data-ttu-id="fbd89-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="fbd89-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="fbd89-470">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="fbd89-471">Pour plus d’informations, consultez <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="fbd89-472">L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :</span><span class="sxs-lookup"><span data-stu-id="fbd89-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="fbd89-473">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="fbd89-473">ConfigureLogging</span></span>

<span data-ttu-id="fbd89-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni.</span><span class="sxs-lookup"><span data-stu-id="fbd89-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="fbd89-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="fbd89-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-476">UseConsoleLifetime</span></span>

<span data-ttu-id="fbd89-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour commencer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="fbd89-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="fbd89-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="fbd89-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="fbd89-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="fbd89-480">La dernière durée de vie inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="fbd89-481">Configuration du conteneur</span><span class="sxs-lookup"><span data-stu-id="fbd89-481">Container configuration</span></span>

<span data-ttu-id="fbd89-482">Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="fbd89-483">L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret.</span><span class="sxs-lookup"><span data-stu-id="fbd89-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="fbd89-484">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="fbd89-485">La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="fbd89-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="fbd89-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="fbd89-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="fbd89-488">Créer un conteneur de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="fbd89-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="fbd89-489">Fournir une fabrique de conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="fbd89-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="fbd89-490">Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :</span><span class="sxs-lookup"><span data-stu-id="fbd89-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="fbd89-491">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="fbd89-491">Extensibility</span></span>

<span data-ttu-id="fbd89-492">L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="fbd89-493">L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="fbd89-494">Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="fbd89-495">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-495">Manage the host</span></span>

<span data-ttu-id="fbd89-496">L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fbd89-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="fbd89-497">Exécuter</span><span class="sxs-lookup"><span data-stu-id="fbd89-497">Run</span></span>

<span data-ttu-id="fbd89-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="fbd89-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-499">RunAsync</span></span>

<span data-ttu-id="fbd89-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :</span><span class="sxs-lookup"><span data-stu-id="fbd89-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="fbd89-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-501">RunConsoleAsync</span></span>

<span data-ttu-id="fbd89-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fbd89-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="fbd89-503">Start et StopAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-503">Start and StopAsync</span></span>

<span data-ttu-id="fbd89-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="fbd89-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="fbd89-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="fbd89-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="fbd89-506">StartAsync et StopAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="fbd89-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="fbd89-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="fbd89-509">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="fbd89-509">WaitForShutdown</span></span>

<span data-ttu-id="fbd89-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché <xref:Microsoft.Extensions.Hosting.IHostLifetime>via le `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , comme (écoute pour <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="fbd89-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="fbd89-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="fbd89-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="fbd89-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="fbd89-514">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="fbd89-514">External control</span></span>

<span data-ttu-id="fbd89-515">Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="fbd89-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="fbd89-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="fbd89-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="fbd89-517">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="fbd89-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="fbd89-518">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="fbd89-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="fbd89-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="fbd89-520">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="fbd89-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="fbd89-521">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="fbd89-522">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="fbd89-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="fbd89-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="fbd89-524">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="fbd89-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="fbd89-525">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="fbd89-525">Cancellation Token</span></span> | <span data-ttu-id="fbd89-526">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="fbd89-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="fbd89-527">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="fbd89-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="fbd89-528">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="fbd89-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="fbd89-529">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="fbd89-529">All requests should be processed.</span></span> <span data-ttu-id="fbd89-530">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="fbd89-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="fbd89-531">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="fbd89-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="fbd89-532">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-532">Requests may still be processing.</span></span> <span data-ttu-id="fbd89-533">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="fbd89-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="fbd89-534">Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe.</span><span class="sxs-lookup"><span data-stu-id="fbd89-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="fbd89-535">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.</span><span class="sxs-lookup"><span data-stu-id="fbd89-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="fbd89-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="fbd89-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="fbd89-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="fbd89-538">La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="fbd89-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fbd89-539">Les modèles ASP.NET Core créent un hôte générique<xref:Microsoft.Extensions.Hosting.HostBuilder>de base .NET ( ).</span><span class="sxs-lookup"><span data-stu-id="fbd89-539">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="fbd89-540">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-540">Host definition</span></span>

<span data-ttu-id="fbd89-541">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="fbd89-541">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="fbd89-542">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="fbd89-542">Dependency injection (DI)</span></span>
* <span data-ttu-id="fbd89-543">Journalisation</span><span class="sxs-lookup"><span data-stu-id="fbd89-543">Logging</span></span>
* <span data-ttu-id="fbd89-544">Configuration</span><span class="sxs-lookup"><span data-stu-id="fbd89-544">Configuration</span></span>
* <span data-ttu-id="fbd89-545">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="fbd89-545">`IHostedService` implementations</span></span>

<span data-ttu-id="fbd89-546">Quand un hôte démarre, il appelle `IHostedService.StartAsync` sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> qu’il trouve dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fbd89-546">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="fbd89-547">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="fbd89-547">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="fbd89-548">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="fbd89-548">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="fbd89-549">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-549">Set up a host</span></span>

<span data-ttu-id="fbd89-550">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-550">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="fbd89-551">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-551">The `Main` method:</span></span>

* <span data-ttu-id="fbd89-552">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="fbd89-552">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="fbd89-553">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="fbd89-553">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="fbd89-554">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-554">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="fbd89-555">Le code suivant crée une charge `IHostedService` de travail non-HTTP avec une implémentation ajoutée au conteneur DI.</span><span class="sxs-lookup"><span data-stu-id="fbd89-555">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="fbd89-556">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-556">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="fbd89-557">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-557">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="fbd89-558">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-558">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="fbd89-559">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="fbd89-559">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="fbd89-560">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="fbd89-560">Default builder settings</span></span>

<span data-ttu-id="fbd89-561">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="fbd89-561">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="fbd89-562">Définit la [racine de](xref:fundamentals/index#content-root) contenu <xref:System.IO.Directory.GetCurrentDirectory*>sur le chemin retourné par .</span><span class="sxs-lookup"><span data-stu-id="fbd89-562">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="fbd89-563">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="fbd89-563">Loads host configuration from:</span></span>
  * <span data-ttu-id="fbd89-564">Variables de l’environnement préfixées avec `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-564">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="fbd89-565">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="fbd89-565">Command-line arguments.</span></span>
* <span data-ttu-id="fbd89-566">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="fbd89-566">Loads app configuration from:</span></span>
  * <span data-ttu-id="fbd89-567">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fbd89-567">*appsettings.json*.</span></span>
  * <span data-ttu-id="fbd89-568">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="fbd89-568">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="fbd89-569">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-569">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="fbd89-570">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-570">Environment variables.</span></span>
  * <span data-ttu-id="fbd89-571">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="fbd89-571">Command-line arguments.</span></span>
* <span data-ttu-id="fbd89-572">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="fbd89-572">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="fbd89-573">Console</span><span class="sxs-lookup"><span data-stu-id="fbd89-573">Console</span></span>
  * <span data-ttu-id="fbd89-574">Débogage</span><span class="sxs-lookup"><span data-stu-id="fbd89-574">Debug</span></span>
  * <span data-ttu-id="fbd89-575">EventSource</span><span class="sxs-lookup"><span data-stu-id="fbd89-575">EventSource</span></span>
  * <span data-ttu-id="fbd89-576">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="fbd89-576">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="fbd89-577">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-577">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="fbd89-578">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-578">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="fbd89-579">Charge la configuration de l’hôte `ASPNETCORE_`à partir de variables de l’environnement préfixées avec .</span><span class="sxs-lookup"><span data-stu-id="fbd89-579">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="fbd89-580">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-580">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="fbd89-581">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-581">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="fbd89-582">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fbd89-582">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="fbd89-583">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-583">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="fbd89-584">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="fbd89-584">Enables IIS integration.</span></span> <span data-ttu-id="fbd89-585">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-585">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="fbd89-586">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-586">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fbd89-587">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="fbd89-587">Framework-provided services</span></span>

<span data-ttu-id="fbd89-588">Les services suivants sont enregistrés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="fbd89-588">The following services are registered automatically:</span></span>

* [<span data-ttu-id="fbd89-589">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-589">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="fbd89-590">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-590">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="fbd89-591">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fbd89-591">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="fbd89-592">Pour plus d’informations sur <xref:fundamentals/dependency-injection#framework-provided-services>les services fournis par le cadre, voir .</span><span class="sxs-lookup"><span data-stu-id="fbd89-592">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="fbd89-593">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-593">IHostApplicationLifetime</span></span>

<span data-ttu-id="fbd89-594">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="fbd89-594">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="fbd89-595">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-595">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="fbd89-596">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-596">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="fbd89-597">L’exemple suivant `IHostedService` est une `IHostApplicationLifetime` implémentation qui enregistre les événements :</span><span class="sxs-lookup"><span data-stu-id="fbd89-597">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="fbd89-598">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fbd89-598">IHostLifetime</span></span>

<span data-ttu-id="fbd89-599">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fbd89-599">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="fbd89-600">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-600">The last implementation registered is used.</span></span>

<span data-ttu-id="fbd89-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="fbd89-602">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="fbd89-602">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="fbd89-603">Écoutez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="fbd89-603">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="fbd89-604">Débloque des extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="fbd89-604">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="fbd89-605">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fbd89-605">IHostEnvironment</span></span>

<span data-ttu-id="fbd89-606">Injecter <xref:Microsoft.Extensions.Hosting.IHostEnvironment> le service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="fbd89-606">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="fbd89-607">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fbd89-607">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="fbd89-608">EnvironmentName (en)</span><span class="sxs-lookup"><span data-stu-id="fbd89-608">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="fbd89-609">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="fbd89-609">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="fbd89-610">Les applications `IWebHostEnvironment` Web implémentent l’interface, qui hérite `IHostEnvironment` et ajoute le [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="fbd89-610">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="fbd89-611">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-611">Host configuration</span></span>

<span data-ttu-id="fbd89-612">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-612">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="fbd89-613">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-613">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="fbd89-614">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-614">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="fbd89-615">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-615">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fbd89-616">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-616">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fbd89-617">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-617">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="fbd89-618">Le fournisseur variable d’environnement avec des arguments de préfixe `DOTNET_` et de commande-ligne sont inclus par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-618">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="fbd89-619">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="fbd89-619">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="fbd89-620">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-620">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="fbd89-621">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-621">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="fbd89-622">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-622">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="fbd89-623">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="fbd89-623">App configuration</span></span>

<span data-ttu-id="fbd89-624">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-624">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fbd89-625">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-625">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fbd89-626">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="fbd89-626">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="fbd89-627">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="fbd89-627">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="fbd89-628">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-628">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="fbd89-629">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="fbd89-629">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="fbd89-630">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="fbd89-630">Settings for all app types</span></span>

<span data-ttu-id="fbd89-631">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbd89-631">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="fbd89-632">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-632">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="fbd89-633">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fbd89-633">ApplicationName</span></span>

<span data-ttu-id="fbd89-634">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-634">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="fbd89-635">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="fbd89-635">**Key**: `applicationName`</span></span>  
<span data-ttu-id="fbd89-636">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-636">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-637">**Défaut**: Nom de l’assemblage qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-637">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="fbd89-638">**Variable environnement**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="fbd89-638">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="fbd89-639">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fbd89-639">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="fbd89-640">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-640">ContentRoot</span></span>

<span data-ttu-id="fbd89-641">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="fbd89-641">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="fbd89-642">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="fbd89-642">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="fbd89-643">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="fbd89-643">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="fbd89-644">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-644">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-645">**Défaut**: Le dossier où se trouve l’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-645">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="fbd89-646">**Variable environnement**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-646">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="fbd89-647">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-647">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="fbd89-648">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-648">For more information, see:</span></span>

* [<span data-ttu-id="fbd89-649">Fondamentaux : Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="fbd89-649">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="fbd89-650">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-650">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="fbd89-651">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="fbd89-651">EnvironmentName</span></span>

<span data-ttu-id="fbd89-652">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="fbd89-652">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="fbd89-653">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-653">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="fbd89-654">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="fbd89-654">Values aren't case-sensitive.</span></span>

<span data-ttu-id="fbd89-655">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="fbd89-655">**Key**: `environment`</span></span>  
<span data-ttu-id="fbd89-656">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-656">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-657">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="fbd89-657">**Default**: `Production`</span></span>  
<span data-ttu-id="fbd89-658">**Variable environnement**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-658">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="fbd89-659">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-659">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="fbd89-660">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="fbd89-660">ShutdownTimeout</span></span>

<span data-ttu-id="fbd89-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="fbd89-662">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="fbd89-662">The default value is five seconds.</span></span>  <span data-ttu-id="fbd89-663">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="fbd89-663">During the timeout period, the host:</span></span>

* <span data-ttu-id="fbd89-664">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="fbd89-664">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="fbd89-665">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="fbd89-665">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="fbd89-666">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fbd89-666">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="fbd89-667">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="fbd89-667">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="fbd89-668">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fbd89-668">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="fbd89-669">**Clé**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="fbd89-669">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="fbd89-670">**Type**:`int`</span><span class="sxs-lookup"><span data-stu-id="fbd89-670">**Type**: `int`</span></span>  
<span data-ttu-id="fbd89-671">**Défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="fbd89-671">**Default**: 5 seconds</span></span>  
<span data-ttu-id="fbd89-672">**Variable environnement**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-672">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="fbd89-673">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-673">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="fbd89-674">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-674">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="fbd89-675">Recharger la configuration de l’application désactiver sur le changement</span><span class="sxs-lookup"><span data-stu-id="fbd89-675">Disable app configuration reload on change</span></span>

<span data-ttu-id="fbd89-676">Par [défaut,](xref:fundamentals/configuration/index#default) *appsettings.json* et *appsettings. Environnement-json* sont rechargés lorsque le fichier change.</span><span class="sxs-lookup"><span data-stu-id="fbd89-676">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="fbd89-677">Pour désactiver ce comportement de recharge dans ASP.NET Core 5.0 Aperçu `hostBuilder:reloadConfigOnChange` 3 `false`ou plus tard, définissez la clé de .</span><span class="sxs-lookup"><span data-stu-id="fbd89-677">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="fbd89-678">**Clé**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="fbd89-678">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="fbd89-679">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-679">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-680">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="fbd89-680">**Default**: `true`</span></span>  
<span data-ttu-id="fbd89-681">**Argument de la ligne de commandement**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="fbd89-681">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="fbd89-682">**Variable environnement**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="fbd89-682">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="fbd89-683">Le séparateur du côlon ()`:`ne fonctionne pas avec des touches hiérarchiques variables de l’environnement sur toutes les plates-formes.</span><span class="sxs-lookup"><span data-stu-id="fbd89-683">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="fbd89-684">Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="fbd89-684">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="fbd89-685">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="fbd89-685">Settings for web apps</span></span>

<span data-ttu-id="fbd89-686">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbd89-686">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="fbd89-687">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-687">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="fbd89-688">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="fbd89-688">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="fbd89-689">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fbd89-689">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="fbd89-690">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="fbd89-690">CaptureStartupErrors</span></span>

<span data-ttu-id="fbd89-691">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-691">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="fbd89-692">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="fbd89-692">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="fbd89-693">**Clé**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="fbd89-693">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="fbd89-694">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-694">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-695">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="fbd89-695">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="fbd89-696">**Variable environnement**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-696">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="fbd89-697">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-697">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="fbd89-698">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="fbd89-698">DetailedErrors</span></span>

<span data-ttu-id="fbd89-699">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="fbd89-699">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="fbd89-700">**Clé**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="fbd89-700">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="fbd89-701">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-701">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-702">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="fbd89-702">**Default**: `false`</span></span>  
<span data-ttu-id="fbd89-703">**Variable environnement**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-703">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="fbd89-704">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-704">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="fbd89-705">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="fbd89-705">HostingStartupAssemblies</span></span>

<span data-ttu-id="fbd89-706">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="fbd89-706">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="fbd89-707">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-707">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="fbd89-708">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="fbd89-708">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="fbd89-709">**Clé**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fbd89-709">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="fbd89-710">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-710">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-711">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="fbd89-711">**Default**: Empty string</span></span>  
<span data-ttu-id="fbd89-712">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fbd89-712">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="fbd89-713">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-713">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="fbd89-714">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="fbd89-714">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="fbd89-715">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="fbd89-715">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="fbd89-716">**Clé**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fbd89-716">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="fbd89-717">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-717">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-718">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="fbd89-718">**Default**: Empty string</span></span>  
<span data-ttu-id="fbd89-719">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fbd89-719">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="fbd89-720">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-720">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="fbd89-721">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="fbd89-721">HTTPS_Port</span></span>

<span data-ttu-id="fbd89-722">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fbd89-722">The HTTPS redirect port.</span></span> <span data-ttu-id="fbd89-723">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="fbd89-723">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fbd89-724">**Clé**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="fbd89-724">**Key**: `https_port`</span></span>  
<span data-ttu-id="fbd89-725">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-725">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-726">**Défaut**: Une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="fbd89-726">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="fbd89-727">**Variable environnement**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-727">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="fbd89-728">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-728">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="fbd89-729">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="fbd89-729">PreferHostingUrls</span></span>

<span data-ttu-id="fbd89-730">Indique si l’hôte doit écouter sur les `IWebHostBuilder` URL configurées avec les `IServer` URL configurées au lieu de ces URL configurées avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="fbd89-730">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="fbd89-731">**Clé**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="fbd89-731">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="fbd89-732">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-732">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-733">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="fbd89-733">**Default**: `true`</span></span>  
<span data-ttu-id="fbd89-734">**Variable environnement**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-734">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="fbd89-735">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-735">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="fbd89-736">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="fbd89-736">PreventHostingStartup</span></span>

<span data-ttu-id="fbd89-737">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-737">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="fbd89-738">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-738">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="fbd89-739">**Clé**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="fbd89-739">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="fbd89-740">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="fbd89-740">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fbd89-741">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="fbd89-741">**Default**: `false`</span></span>  
<span data-ttu-id="fbd89-742">**Variable environnement**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="fbd89-742">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="fbd89-743">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-743">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="fbd89-744">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="fbd89-744">StartupAssembly</span></span>

<span data-ttu-id="fbd89-745">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-745">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="fbd89-746">**Clé**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="fbd89-746">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="fbd89-747">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-747">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-748">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="fbd89-748">**Default**: The app's assembly</span></span>  
<span data-ttu-id="fbd89-749">**Variable environnement**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="fbd89-749">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="fbd89-750">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-750">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="fbd89-751">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="fbd89-751">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="fbd89-752">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="fbd89-752">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="fbd89-753">URLs</span><span class="sxs-lookup"><span data-stu-id="fbd89-753">URLs</span></span>

<span data-ttu-id="fbd89-754">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="fbd89-754">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="fbd89-755">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="fbd89-755">For example, `http://localhost:123`.</span></span> <span data-ttu-id="fbd89-756">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="fbd89-756">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="fbd89-757">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="fbd89-757">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="fbd89-758">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="fbd89-758">Supported formats vary among servers.</span></span>

<span data-ttu-id="fbd89-759">**Clé**:`urls`</span><span class="sxs-lookup"><span data-stu-id="fbd89-759">**Key**: `urls`</span></span>  
<span data-ttu-id="fbd89-760">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-760">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-761">**Défaut** `http://localhost:5000` : et`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="fbd89-761">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="fbd89-762">**Variable environnement**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="fbd89-762">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="fbd89-763">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-763">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="fbd89-764">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="fbd89-764">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="fbd89-765">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-765">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="fbd89-766">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-766">WebRoot</span></span>

<span data-ttu-id="fbd89-767">La propriété [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin relatif vers les actifs statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-767">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="fbd89-768">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="fbd89-768">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="fbd89-769">**Clé**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="fbd89-769">**Key**: `webroot`</span></span>  
<span data-ttu-id="fbd89-770">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="fbd89-770">**Type**: `string`</span></span>  
<span data-ttu-id="fbd89-771">**Défaut**: La `wwwroot`valeur par défaut est .</span><span class="sxs-lookup"><span data-stu-id="fbd89-771">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="fbd89-772">Le chemin vers *la « racine de contenu »/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="fbd89-772">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="fbd89-773">**Variable environnement**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="fbd89-773">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="fbd89-774">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="fbd89-774">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="fbd89-775">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="fbd89-775">For more information, see:</span></span>

* [<span data-ttu-id="fbd89-776">Fondamentaux : racine web</span><span class="sxs-lookup"><span data-stu-id="fbd89-776">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="fbd89-777">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fbd89-777">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="fbd89-778">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="fbd89-778">Manage the host lifetime</span></span>

<span data-ttu-id="fbd89-779">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="fbd89-779">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="fbd89-780">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fbd89-780">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="fbd89-781">Exécuter</span><span class="sxs-lookup"><span data-stu-id="fbd89-781">Run</span></span>

<span data-ttu-id="fbd89-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="fbd89-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="fbd89-783">RunAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-783">RunAsync</span></span>

<span data-ttu-id="fbd89-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="fbd89-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="fbd89-785">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-785">RunConsoleAsync</span></span>

<span data-ttu-id="fbd89-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="fbd89-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="fbd89-787">Démarrer</span><span class="sxs-lookup"><span data-stu-id="fbd89-787">Start</span></span>

<span data-ttu-id="fbd89-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="fbd89-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="fbd89-789">StartAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-789">StartAsync</span></span>

<span data-ttu-id="fbd89-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="fbd89-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="fbd89-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="fbd89-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="fbd89-792">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="fbd89-792">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="fbd89-793">StopAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-793">StopAsync</span></span>

<span data-ttu-id="fbd89-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="fbd89-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="fbd89-795">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="fbd89-795">WaitForShutdown</span></span>

<span data-ttu-id="fbd89-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le fil d’appel jusqu’à ce que l’arrêt soit déclenché par l’IHostLifetime, par exemple via <kbd>Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="fbd89-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="fbd89-797">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="fbd89-797">WaitForShutdownAsync</span></span>

<span data-ttu-id="fbd89-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fbd89-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="fbd89-799">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="fbd89-799">External control</span></span>

<span data-ttu-id="fbd89-800">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="fbd89-800">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="fbd89-801">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fbd89-801">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
