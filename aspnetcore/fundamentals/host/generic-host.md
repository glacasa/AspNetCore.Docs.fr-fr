---
title: Hôte générique .NET
author: rick-anderson
description: Découvrez l’hôte générique .NET Core, qui est responsable de la gestion du démarrage et de la durée de vie des applications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 46a56c278e889778e58a1fbb41ec217aaf023b13
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488772"
---
# <a name="net-generic-host"></a><span data-ttu-id="cdd89-103">Hôte générique .NET</span><span class="sxs-lookup"><span data-stu-id="cdd89-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="cdd89-104">Les modèles ASP.NET Core créent un hôte générique <xref:Microsoft.Extensions.Hosting.HostBuilder>de base .NET, .</span><span class="sxs-lookup"><span data-stu-id="cdd89-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="cdd89-105">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-105">Host definition</span></span>

<span data-ttu-id="cdd89-106">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="cdd89-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="cdd89-107">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="cdd89-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="cdd89-108">Journalisation</span><span class="sxs-lookup"><span data-stu-id="cdd89-108">Logging</span></span>
* <span data-ttu-id="cdd89-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="cdd89-109">Configuration</span></span>
* <span data-ttu-id="cdd89-110">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="cdd89-110">`IHostedService` implementations</span></span>

<span data-ttu-id="cdd89-111">Quand un hôte démarre, il appelle `IHostedService.StartAsync` sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> qu’il trouve dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="cdd89-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="cdd89-112">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="cdd89-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="cdd89-113">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="cdd89-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="cdd89-114">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-114">Set up a host</span></span>

<span data-ttu-id="cdd89-115">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="cdd89-116">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-116">The `Main` method:</span></span>

* <span data-ttu-id="cdd89-117">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="cdd89-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="cdd89-118">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="cdd89-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="cdd89-119">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte générique :</span><span class="sxs-lookup"><span data-stu-id="cdd89-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="cdd89-120">Le code suivant crée un hôte générique en utilisant la charge de travail non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd89-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="cdd89-121">La `IHostedService` mise en œuvre est ajoutée au conteneur DI :</span><span class="sxs-lookup"><span data-stu-id="cdd89-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="cdd89-122">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="cdd89-123">Le code précédent est généré par les modèles ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdd89-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="cdd89-124">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="cdd89-125">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="cdd89-126">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="cdd89-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="cdd89-127">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="cdd89-127">Default builder settings</span></span>

<span data-ttu-id="cdd89-128">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="cdd89-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="cdd89-129">Définit la [racine de](xref:fundamentals/index#content-root) contenu <xref:System.IO.Directory.GetCurrentDirectory*>sur le chemin retourné par .</span><span class="sxs-lookup"><span data-stu-id="cdd89-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="cdd89-130">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="cdd89-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="cdd89-131">Variables de l’environnement préfixées avec `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="cdd89-132">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="cdd89-132">Command-line arguments.</span></span>
* <span data-ttu-id="cdd89-133">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="cdd89-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="cdd89-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cdd89-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="cdd89-135">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="cdd89-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="cdd89-136">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="cdd89-137">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-137">Environment variables.</span></span>
  * <span data-ttu-id="cdd89-138">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="cdd89-138">Command-line arguments.</span></span>
* <span data-ttu-id="cdd89-139">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="cdd89-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="cdd89-140">Console</span><span class="sxs-lookup"><span data-stu-id="cdd89-140">Console</span></span>
  * <span data-ttu-id="cdd89-141">Débogage</span><span class="sxs-lookup"><span data-stu-id="cdd89-141">Debug</span></span>
  * <span data-ttu-id="cdd89-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="cdd89-142">EventSource</span></span>
  * <span data-ttu-id="cdd89-143">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="cdd89-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="cdd89-144">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="cdd89-145">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="cdd89-146">Charge la configuration de l’hôte `ASPNETCORE_`à partir de variables de l’environnement préfixées avec .</span><span class="sxs-lookup"><span data-stu-id="cdd89-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="cdd89-147">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="cdd89-148">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="cdd89-149">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="cdd89-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="cdd89-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="cdd89-151">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="cdd89-151">Enables IIS integration.</span></span> <span data-ttu-id="cdd89-152">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="cdd89-153">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="cdd89-154">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="cdd89-154">Framework-provided services</span></span>

<span data-ttu-id="cdd89-155">Les services suivants sont enregistrés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="cdd89-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="cdd89-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="cdd89-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="cdd89-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="cdd89-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="cdd89-159">Pour plus d’informations sur <xref:fundamentals/dependency-injection#framework-provided-services>les services fournis par le cadre, voir .</span><span class="sxs-lookup"><span data-stu-id="cdd89-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="cdd89-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="cdd89-161">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="cdd89-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="cdd89-162">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="cdd89-163">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="cdd89-164">L’exemple suivant `IHostedService` est une `IHostApplicationLifetime` implémentation qui enregistre les événements :</span><span class="sxs-lookup"><span data-stu-id="cdd89-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="cdd89-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-165">IHostLifetime</span></span>

<span data-ttu-id="cdd89-166">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="cdd89-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="cdd89-167">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-167">The last implementation registered is used.</span></span>

<span data-ttu-id="cdd89-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="cdd89-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="cdd89-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="cdd89-170">Écoutez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="cdd89-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="cdd89-171">Débloque des extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="cdd89-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="cdd89-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="cdd89-172">IHostEnvironment</span></span>

<span data-ttu-id="cdd89-173">Injecter <xref:Microsoft.Extensions.Hosting.IHostEnvironment> le service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="cdd89-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="cdd89-174">Applicationname</span><span class="sxs-lookup"><span data-stu-id="cdd89-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="cdd89-175">EnvironmentName (en)</span><span class="sxs-lookup"><span data-stu-id="cdd89-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="cdd89-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="cdd89-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="cdd89-177">Les applications `IWebHostEnvironment` Web implémentent l’interface, qui hérite `IHostEnvironment` et ajoute le [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="cdd89-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="cdd89-178">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-178">Host configuration</span></span>

<span data-ttu-id="cdd89-179">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="cdd89-180">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="cdd89-181">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="cdd89-182">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="cdd89-183">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="cdd89-184">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="cdd89-185">Le fournisseur variable d’environnement avec des arguments de préfixe `DOTNET_` et de commande-ligne sont inclus par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="cdd89-186">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="cdd89-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="cdd89-187">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="cdd89-188">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="cdd89-189">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="cdd89-190">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="cdd89-190">App configuration</span></span>

<span data-ttu-id="cdd89-191">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="cdd89-192">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="cdd89-193">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="cdd89-194">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="cdd89-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="cdd89-195">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="cdd89-196">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="cdd89-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="cdd89-197">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="cdd89-197">Settings for all app types</span></span>

<span data-ttu-id="cdd89-198">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd89-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="cdd89-199">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="cdd89-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="cdd89-200">ApplicationName</span></span>

<span data-ttu-id="cdd89-201">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="cdd89-202">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="cdd89-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="cdd89-203">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-203">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-204">**Défaut**: Nom de l’assemblage qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="cdd89-205">**Variable environnement**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="cdd89-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="cdd89-206">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="cdd89-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-207">ContentRoot</span></span>

<span data-ttu-id="cdd89-208">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="cdd89-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="cdd89-209">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="cdd89-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="cdd89-210">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="cdd89-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="cdd89-211">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-211">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-212">**Défaut**: Le dossier où se trouve l’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="cdd89-213">**Variable environnement**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="cdd89-214">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="cdd89-215">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-215">For more information, see:</span></span>

* [<span data-ttu-id="cdd89-216">Fondamentaux : Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="cdd89-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="cdd89-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="cdd89-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="cdd89-218">EnvironmentName</span></span>

<span data-ttu-id="cdd89-219">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="cdd89-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="cdd89-220">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="cdd89-221">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="cdd89-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="cdd89-222">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="cdd89-222">**Key**: `environment`</span></span>  
<span data-ttu-id="cdd89-223">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-223">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-224">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="cdd89-224">**Default**: `Production`</span></span>  
<span data-ttu-id="cdd89-225">**Variable environnement**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="cdd89-226">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="cdd89-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="cdd89-227">ShutdownTimeout</span></span>

<span data-ttu-id="cdd89-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="cdd89-229">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="cdd89-229">The default value is five seconds.</span></span>  <span data-ttu-id="cdd89-230">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="cdd89-231">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="cdd89-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="cdd89-232">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="cdd89-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="cdd89-233">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="cdd89-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="cdd89-234">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="cdd89-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="cdd89-235">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="cdd89-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="cdd89-236">**Clé**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="cdd89-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="cdd89-237">**Type**:`int`</span><span class="sxs-lookup"><span data-stu-id="cdd89-237">**Type**: `int`</span></span>  
<span data-ttu-id="cdd89-238">**Défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="cdd89-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="cdd89-239">**Variable environnement**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="cdd89-240">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="cdd89-241">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="cdd89-242">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="cdd89-242">Settings for web apps</span></span>

<span data-ttu-id="cdd89-243">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd89-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="cdd89-244">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="cdd89-245">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="cdd89-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="cdd89-246">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="cdd89-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="cdd89-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="cdd89-247">CaptureStartupErrors</span></span>

<span data-ttu-id="cdd89-248">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="cdd89-249">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="cdd89-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="cdd89-250">**Clé**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="cdd89-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="cdd89-251">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-252">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="cdd89-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="cdd89-253">**Variable environnement**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="cdd89-254">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="cdd89-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="cdd89-255">DetailedErrors</span></span>

<span data-ttu-id="cdd89-256">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="cdd89-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="cdd89-257">**Clé**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="cdd89-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="cdd89-258">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-259">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="cdd89-259">**Default**: `false`</span></span>  
<span data-ttu-id="cdd89-260">**Variable environnement**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="cdd89-261">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="cdd89-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="cdd89-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="cdd89-263">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="cdd89-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="cdd89-264">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="cdd89-265">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="cdd89-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="cdd89-266">**Clé**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="cdd89-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="cdd89-267">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-267">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-268">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="cdd89-268">**Default**: Empty string</span></span>  
<span data-ttu-id="cdd89-269">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="cdd89-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="cdd89-270">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="cdd89-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="cdd89-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="cdd89-272">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="cdd89-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="cdd89-273">**Clé**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="cdd89-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="cdd89-274">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-274">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-275">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="cdd89-275">**Default**: Empty string</span></span>  
<span data-ttu-id="cdd89-276">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="cdd89-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="cdd89-277">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="cdd89-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="cdd89-278">HTTPS_Port</span></span>

<span data-ttu-id="cdd89-279">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cdd89-279">The HTTPS redirect port.</span></span> <span data-ttu-id="cdd89-280">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="cdd89-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="cdd89-281">**Clé**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="cdd89-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="cdd89-282">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-282">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-283">**Défaut**: Une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="cdd89-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="cdd89-284">**Variable environnement**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="cdd89-285">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="cdd89-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="cdd89-286">PreferHostingUrls</span></span>

<span data-ttu-id="cdd89-287">Indique si l’hôte doit écouter sur les `IWebHostBuilder` URL configurées avec les `IServer` URL configurées au lieu de ces URL configurées avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="cdd89-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="cdd89-288">**Clé**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="cdd89-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="cdd89-289">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-290">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="cdd89-290">**Default**: `true`</span></span>  
<span data-ttu-id="cdd89-291">**Variable environnement**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="cdd89-292">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="cdd89-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="cdd89-293">PreventHostingStartup</span></span>

<span data-ttu-id="cdd89-294">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="cdd89-295">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="cdd89-296">**Clé**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="cdd89-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="cdd89-297">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-298">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="cdd89-298">**Default**: `false`</span></span>  
<span data-ttu-id="cdd89-299">**Variable environnement**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="cdd89-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="cdd89-300">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="cdd89-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="cdd89-301">StartupAssembly</span></span>

<span data-ttu-id="cdd89-302">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="cdd89-303">**Clé**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="cdd89-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="cdd89-304">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-304">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-305">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="cdd89-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="cdd89-306">**Variable environnement**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="cdd89-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="cdd89-307">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="cdd89-308">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="cdd89-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="cdd89-309">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="cdd89-310">URLs</span><span class="sxs-lookup"><span data-stu-id="cdd89-310">URLs</span></span>

<span data-ttu-id="cdd89-311">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="cdd89-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="cdd89-312">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="cdd89-313">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="cdd89-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="cdd89-314">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="cdd89-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="cdd89-315">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="cdd89-316">**Clé**:`urls`</span><span class="sxs-lookup"><span data-stu-id="cdd89-316">**Key**: `urls`</span></span>  
<span data-ttu-id="cdd89-317">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-317">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-318">**Défaut** `http://localhost:5000` : et`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="cdd89-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="cdd89-319">**Variable environnement**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="cdd89-320">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="cdd89-321">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="cdd89-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="cdd89-322">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="cdd89-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-323">WebRoot</span></span>

<span data-ttu-id="cdd89-324">La propriété [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin relatif vers les actifs statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="cdd89-325">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="cdd89-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="cdd89-326">**Clé**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="cdd89-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="cdd89-327">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-327">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-328">**Défaut**: La `wwwroot`valeur par défaut est .</span><span class="sxs-lookup"><span data-stu-id="cdd89-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="cdd89-329">Le chemin vers *la « racine de contenu »/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="cdd89-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="cdd89-330">**Variable environnement**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="cdd89-331">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="cdd89-332">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-332">For more information, see:</span></span>

* [<span data-ttu-id="cdd89-333">Fondamentaux : racine web</span><span class="sxs-lookup"><span data-stu-id="cdd89-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="cdd89-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="cdd89-335">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-335">Manage the host lifetime</span></span>

<span data-ttu-id="cdd89-336">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="cdd89-337">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="cdd89-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="cdd89-338">Exécuter</span><span class="sxs-lookup"><span data-stu-id="cdd89-338">Run</span></span>

<span data-ttu-id="cdd89-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="cdd89-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-340">RunAsync</span></span>

<span data-ttu-id="cdd89-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="cdd89-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="cdd89-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-342">RunConsoleAsync</span></span>

<span data-ttu-id="cdd89-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="cdd89-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="cdd89-344">Démarrer</span><span class="sxs-lookup"><span data-stu-id="cdd89-344">Start</span></span>

<span data-ttu-id="cdd89-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="cdd89-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="cdd89-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-346">StartAsync</span></span>

<span data-ttu-id="cdd89-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="cdd89-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="cdd89-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="cdd89-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="cdd89-349">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="cdd89-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="cdd89-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-350">StopAsync</span></span>

<span data-ttu-id="cdd89-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="cdd89-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="cdd89-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="cdd89-352">WaitForShutdown</span></span>

<span data-ttu-id="cdd89-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le fil d’appel jusqu’à ce que l’arrêt soit déclenché par l’IHostLifetime, par exemple via <kbd>Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="cdd89-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="cdd89-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="cdd89-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="cdd89-356">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="cdd89-356">External control</span></span>

<span data-ttu-id="cdd89-357">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="cdd89-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="cdd89-358">Les applications ASP.NET Core configurent et lancent un hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="cdd89-359">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="cdd89-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="cdd89-360">Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd89-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="cdd89-361">L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="cdd89-362">La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="cdd89-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="cdd89-363">L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web.</span><span class="sxs-lookup"><span data-stu-id="cdd89-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="cdd89-364">Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="cdd89-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="cdd89-365">L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd89-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="cdd89-366">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cdd89-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cdd89-367">Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré*.</span><span class="sxs-lookup"><span data-stu-id="cdd89-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="cdd89-368">N’exécutez pas l’exemple dans une `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="cdd89-369">Pour définir la console dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="cdd89-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="cdd89-370">Ouvrez le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="cdd89-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="cdd89-371">Dans la configuration **.NET Core Launch (console)**, recherchez l’entrée **console**.</span><span class="sxs-lookup"><span data-stu-id="cdd89-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="cdd89-372">Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="cdd89-373">Introduction</span><span class="sxs-lookup"><span data-stu-id="cdd89-373">Introduction</span></span>

<span data-ttu-id="cdd89-374">La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="cdd89-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="cdd89-375">Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).</span><span class="sxs-lookup"><span data-stu-id="cdd89-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="cdd89-376"><xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code.</span><span class="sxs-lookup"><span data-stu-id="cdd89-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="cdd89-377">Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="cdd89-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="cdd89-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="cdd89-379">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-379">Set up a host</span></span>

<span data-ttu-id="cdd89-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="cdd89-381">Options</span><span class="sxs-lookup"><span data-stu-id="cdd89-381">Options</span></span>

<span data-ttu-id="cdd89-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="cdd89-383">Délai d’expiration</span><span class="sxs-lookup"><span data-stu-id="cdd89-383">Shutdown timeout</span></span>

<span data-ttu-id="cdd89-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="cdd89-385">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="cdd89-385">The default value is five seconds.</span></span>

<span data-ttu-id="cdd89-386">La configuration d’option suivante augmente `Program.Main` le délai d’arrêt par défaut de cinq secondes à 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="cdd89-387">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="cdd89-387">Default services</span></span>

<span data-ttu-id="cdd89-388">Les services suivants sont inscrits au moment de l’initialisation de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="cdd89-389">[Environnement](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="cdd89-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="cdd89-390">[Configuration](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="cdd89-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="cdd89-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="cdd89-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="cdd89-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="cdd89-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="cdd89-393">[Options](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="cdd89-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="cdd89-394">[Enregistrement](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="cdd89-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="cdd89-395">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-395">Host configuration</span></span>

<span data-ttu-id="cdd89-396">La création de la configuration d’hôte fait appel aux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-396">Host configuration is created by:</span></span>

* <span data-ttu-id="cdd89-397">Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="cdd89-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="cdd89-398">Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="cdd89-399">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="cdd89-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="cdd89-400">Clé d’application (nom)</span><span class="sxs-lookup"><span data-stu-id="cdd89-400">Application key (name)</span></span>

<span data-ttu-id="cdd89-401">La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="cdd89-402">Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :</span><span class="sxs-lookup"><span data-stu-id="cdd89-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="cdd89-403">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="cdd89-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="cdd89-404">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-404">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-405">**Par défaut** : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="cdd89-406">**Ensemble à l’aide**de :`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="cdd89-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="cdd89-407">**Variable de l’environnement**: `<PREFIX_>APPLICATIONNAME` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="cdd89-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="cdd89-408">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="cdd89-408">Content root</span></span>

<span data-ttu-id="cdd89-409">Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="cdd89-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="cdd89-410">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="cdd89-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="cdd89-411">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-411">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-412">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="cdd89-413">**Ensemble à l’aide**de :`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="cdd89-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="cdd89-414">**Variable de l’environnement**: `<PREFIX_>CONTENTROOT` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="cdd89-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="cdd89-415">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="cdd89-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="cdd89-416">Pour plus d’informations, voir [Fundamentals: Content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="cdd89-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="cdd89-417">Environnement</span><span class="sxs-lookup"><span data-stu-id="cdd89-417">Environment</span></span>

<span data-ttu-id="cdd89-418">Définit [l’environnement](xref:fundamentals/environments)de l’application .</span><span class="sxs-lookup"><span data-stu-id="cdd89-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="cdd89-419">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="cdd89-419">**Key**: `environment`</span></span>  
<span data-ttu-id="cdd89-420">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-420">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-421">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="cdd89-421">**Default**: `Production`</span></span>  
<span data-ttu-id="cdd89-422">**Ensemble à l’aide**de :`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="cdd89-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="cdd89-423">**Variable de l’environnement**: `<PREFIX_>ENVIRONMENT` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="cdd89-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="cdd89-424">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="cdd89-424">The environment can be set to any value.</span></span> <span data-ttu-id="cdd89-425">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="cdd89-426">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="cdd89-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="cdd89-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="cdd89-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="cdd89-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="cdd89-429">La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="cdd89-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="cdd89-431">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="cdd89-432">Aucun fournisseur n’est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-432">No providers are included by default.</span></span> <span data-ttu-id="cdd89-433">Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :</span><span class="sxs-lookup"><span data-stu-id="cdd89-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="cdd89-434">Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="cdd89-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="cdd89-435">Configuration de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-435">Environment variable configuration.</span></span>
* <span data-ttu-id="cdd89-436">Configuration de l’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="cdd89-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="cdd89-437">Tout autre fournisseur de configuration obligatoire.</span><span class="sxs-lookup"><span data-stu-id="cdd89-437">Any other required configuration providers.</span></span>

<span data-ttu-id="cdd89-438">Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cdd89-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="cdd89-439">L’exemple d’application utilise un fichier JSON, *hostsettings.json*, et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.</span><span class="sxs-lookup"><span data-stu-id="cdd89-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="cdd89-440">Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="cdd89-441">`AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cdd89-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="cdd89-442">L’exemple d’application utilise un préfixe `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="cdd89-443">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="cdd89-444">Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="cdd89-445">Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cdd89-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="cdd89-446">Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="cdd89-447">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cdd89-448">Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="cdd89-449">La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="cdd89-450">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="cdd89-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="cdd89-451">Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="cdd89-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="cdd89-452">Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="cdd89-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="cdd89-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="cdd89-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="cdd89-454">Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="cdd89-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="cdd89-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="cdd89-457">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="cdd89-458">La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="cdd89-459">La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="cdd89-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="cdd89-460">Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="cdd89-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="cdd89-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cdd89-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="cdd89-462">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="cdd89-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="cdd89-463">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="cdd89-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="cdd89-464">Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="cdd89-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="cdd89-465">L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :</span><span class="sxs-lookup"><span data-stu-id="cdd89-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="cdd89-466">Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="cdd89-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="cdd89-467">Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="cdd89-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="cdd89-468">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="cdd89-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="cdd89-469">ConfigureServices</span></span>

<span data-ttu-id="cdd89-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cdd89-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="cdd89-472">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="cdd89-473">Pour plus d’informations, consultez <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="cdd89-474">L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :</span><span class="sxs-lookup"><span data-stu-id="cdd89-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="cdd89-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="cdd89-475">ConfigureLogging</span></span>

<span data-ttu-id="cdd89-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni.</span><span class="sxs-lookup"><span data-stu-id="cdd89-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="cdd89-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="cdd89-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-478">UseConsoleLifetime</span></span>

<span data-ttu-id="cdd89-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour commencer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="cdd89-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="cdd89-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="cdd89-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="cdd89-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="cdd89-482">La dernière durée de vie inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="cdd89-483">Configuration du conteneur</span><span class="sxs-lookup"><span data-stu-id="cdd89-483">Container configuration</span></span>

<span data-ttu-id="cdd89-484">Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="cdd89-485">L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret.</span><span class="sxs-lookup"><span data-stu-id="cdd89-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="cdd89-486">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="cdd89-487">La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="cdd89-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="cdd89-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="cdd89-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="cdd89-490">Créer un conteneur de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="cdd89-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="cdd89-491">Fournir une fabrique de conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="cdd89-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="cdd89-492">Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :</span><span class="sxs-lookup"><span data-stu-id="cdd89-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="cdd89-493">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="cdd89-493">Extensibility</span></span>

<span data-ttu-id="cdd89-494">L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="cdd89-495">L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="cdd89-496">Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="cdd89-497">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-497">Manage the host</span></span>

<span data-ttu-id="cdd89-498">L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="cdd89-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="cdd89-499">Exécuter</span><span class="sxs-lookup"><span data-stu-id="cdd89-499">Run</span></span>

<span data-ttu-id="cdd89-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="cdd89-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-501">RunAsync</span></span>

<span data-ttu-id="cdd89-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :</span><span class="sxs-lookup"><span data-stu-id="cdd89-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="cdd89-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-503">RunConsoleAsync</span></span>

<span data-ttu-id="cdd89-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="cdd89-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="cdd89-505">Start et StopAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-505">Start and StopAsync</span></span>

<span data-ttu-id="cdd89-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="cdd89-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="cdd89-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="cdd89-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="cdd89-508">StartAsync et StopAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="cdd89-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="cdd89-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="cdd89-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="cdd89-511">WaitForShutdown</span></span>

<span data-ttu-id="cdd89-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché <xref:Microsoft.Extensions.Hosting.IHostLifetime>via le `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , comme (écoute pour <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="cdd89-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="cdd89-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="cdd89-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="cdd89-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="cdd89-516">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="cdd89-516">External control</span></span>

<span data-ttu-id="cdd89-517">Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="cdd89-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="cdd89-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="cdd89-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="cdd89-519">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="cdd89-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="cdd89-520">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="cdd89-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="cdd89-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="cdd89-522">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="cdd89-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="cdd89-523">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="cdd89-524">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="cdd89-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="cdd89-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="cdd89-526">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="cdd89-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="cdd89-527">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="cdd89-527">Cancellation Token</span></span> | <span data-ttu-id="cdd89-528">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="cdd89-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="cdd89-529">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="cdd89-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="cdd89-530">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="cdd89-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="cdd89-531">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="cdd89-531">All requests should be processed.</span></span> <span data-ttu-id="cdd89-532">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="cdd89-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="cdd89-533">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="cdd89-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="cdd89-534">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-534">Requests may still be processing.</span></span> <span data-ttu-id="cdd89-535">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="cdd89-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="cdd89-536">Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe.</span><span class="sxs-lookup"><span data-stu-id="cdd89-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="cdd89-537">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.</span><span class="sxs-lookup"><span data-stu-id="cdd89-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="cdd89-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="cdd89-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="cdd89-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="cdd89-540">La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="cdd89-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="cdd89-541">Les modèles ASP.NET Core créent un hôte générique<xref:Microsoft.Extensions.Hosting.HostBuilder>de base .NET ( ).</span><span class="sxs-lookup"><span data-stu-id="cdd89-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="cdd89-542">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-542">Host definition</span></span>

<span data-ttu-id="cdd89-543">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="cdd89-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="cdd89-544">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="cdd89-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="cdd89-545">Journalisation</span><span class="sxs-lookup"><span data-stu-id="cdd89-545">Logging</span></span>
* <span data-ttu-id="cdd89-546">Configuration</span><span class="sxs-lookup"><span data-stu-id="cdd89-546">Configuration</span></span>
* <span data-ttu-id="cdd89-547">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="cdd89-547">`IHostedService` implementations</span></span>

<span data-ttu-id="cdd89-548">Quand un hôte démarre, il appelle `IHostedService.StartAsync` sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> qu’il trouve dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="cdd89-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="cdd89-549">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="cdd89-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="cdd89-550">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="cdd89-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="cdd89-551">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-551">Set up a host</span></span>

<span data-ttu-id="cdd89-552">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="cdd89-553">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-553">The `Main` method:</span></span>

* <span data-ttu-id="cdd89-554">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="cdd89-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="cdd89-555">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="cdd89-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="cdd89-556">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="cdd89-557">Le code suivant crée une charge `IHostedService` de travail non-HTTP avec une implémentation ajoutée au conteneur DI.</span><span class="sxs-lookup"><span data-stu-id="cdd89-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="cdd89-558">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="cdd89-559">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="cdd89-560">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="cdd89-561">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="cdd89-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="cdd89-562">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="cdd89-562">Default builder settings</span></span>

<span data-ttu-id="cdd89-563">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="cdd89-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="cdd89-564">Définit la [racine de](xref:fundamentals/index#content-root) contenu <xref:System.IO.Directory.GetCurrentDirectory*>sur le chemin retourné par .</span><span class="sxs-lookup"><span data-stu-id="cdd89-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="cdd89-565">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="cdd89-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="cdd89-566">Variables de l’environnement préfixées avec `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="cdd89-567">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="cdd89-567">Command-line arguments.</span></span>
* <span data-ttu-id="cdd89-568">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="cdd89-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="cdd89-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cdd89-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="cdd89-570">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="cdd89-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="cdd89-571">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="cdd89-572">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-572">Environment variables.</span></span>
  * <span data-ttu-id="cdd89-573">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="cdd89-573">Command-line arguments.</span></span>
* <span data-ttu-id="cdd89-574">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="cdd89-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="cdd89-575">Console</span><span class="sxs-lookup"><span data-stu-id="cdd89-575">Console</span></span>
  * <span data-ttu-id="cdd89-576">Débogage</span><span class="sxs-lookup"><span data-stu-id="cdd89-576">Debug</span></span>
  * <span data-ttu-id="cdd89-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="cdd89-577">EventSource</span></span>
  * <span data-ttu-id="cdd89-578">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="cdd89-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="cdd89-579">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="cdd89-580">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="cdd89-581">Charge la configuration de l’hôte `ASPNETCORE_`à partir de variables de l’environnement préfixées avec .</span><span class="sxs-lookup"><span data-stu-id="cdd89-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="cdd89-582">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="cdd89-583">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="cdd89-584">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="cdd89-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="cdd89-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="cdd89-586">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="cdd89-586">Enables IIS integration.</span></span> <span data-ttu-id="cdd89-587">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="cdd89-588">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="cdd89-589">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="cdd89-589">Framework-provided services</span></span>

<span data-ttu-id="cdd89-590">Les services suivants sont enregistrés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="cdd89-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="cdd89-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="cdd89-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="cdd89-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="cdd89-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="cdd89-594">Pour plus d’informations sur <xref:fundamentals/dependency-injection#framework-provided-services>les services fournis par le cadre, voir .</span><span class="sxs-lookup"><span data-stu-id="cdd89-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="cdd89-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="cdd89-596">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="cdd89-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="cdd89-597">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="cdd89-598">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="cdd89-599">L’exemple suivant `IHostedService` est une `IHostApplicationLifetime` implémentation qui enregistre les événements :</span><span class="sxs-lookup"><span data-stu-id="cdd89-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="cdd89-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="cdd89-600">IHostLifetime</span></span>

<span data-ttu-id="cdd89-601">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="cdd89-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="cdd89-602">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-602">The last implementation registered is used.</span></span>

<span data-ttu-id="cdd89-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="cdd89-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="cdd89-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="cdd89-605">Écoutez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="cdd89-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="cdd89-606">Débloque des extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="cdd89-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="cdd89-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="cdd89-607">IHostEnvironment</span></span>

<span data-ttu-id="cdd89-608">Injecter <xref:Microsoft.Extensions.Hosting.IHostEnvironment> le service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="cdd89-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="cdd89-609">Applicationname</span><span class="sxs-lookup"><span data-stu-id="cdd89-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="cdd89-610">EnvironmentName (en)</span><span class="sxs-lookup"><span data-stu-id="cdd89-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="cdd89-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="cdd89-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="cdd89-612">Les applications `IWebHostEnvironment` Web implémentent l’interface, qui hérite `IHostEnvironment` et ajoute le [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="cdd89-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="cdd89-613">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-613">Host configuration</span></span>

<span data-ttu-id="cdd89-614">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="cdd89-615">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="cdd89-616">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="cdd89-617">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="cdd89-618">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="cdd89-619">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="cdd89-620">Le fournisseur variable d’environnement avec des arguments de préfixe `DOTNET_` et de commande-ligne sont inclus par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="cdd89-621">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="cdd89-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="cdd89-622">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="cdd89-623">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="cdd89-624">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="cdd89-625">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="cdd89-625">App configuration</span></span>

<span data-ttu-id="cdd89-626">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="cdd89-627">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="cdd89-628">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="cdd89-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="cdd89-629">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="cdd89-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="cdd89-630">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="cdd89-631">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="cdd89-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="cdd89-632">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="cdd89-632">Settings for all app types</span></span>

<span data-ttu-id="cdd89-633">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd89-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="cdd89-634">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="cdd89-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="cdd89-635">ApplicationName</span></span>

<span data-ttu-id="cdd89-636">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="cdd89-637">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="cdd89-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="cdd89-638">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-638">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-639">**Défaut**: Nom de l’assemblage qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="cdd89-640">**Variable environnement**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="cdd89-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="cdd89-641">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="cdd89-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="cdd89-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-642">ContentRoot</span></span>

<span data-ttu-id="cdd89-643">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="cdd89-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="cdd89-644">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="cdd89-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="cdd89-645">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="cdd89-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="cdd89-646">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-646">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-647">**Défaut**: Le dossier où se trouve l’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="cdd89-648">**Variable environnement**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="cdd89-649">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="cdd89-650">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-650">For more information, see:</span></span>

* [<span data-ttu-id="cdd89-651">Fondamentaux : Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="cdd89-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="cdd89-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="cdd89-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="cdd89-653">EnvironmentName</span></span>

<span data-ttu-id="cdd89-654">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="cdd89-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="cdd89-655">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="cdd89-656">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="cdd89-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="cdd89-657">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="cdd89-657">**Key**: `environment`</span></span>  
<span data-ttu-id="cdd89-658">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-658">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-659">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="cdd89-659">**Default**: `Production`</span></span>  
<span data-ttu-id="cdd89-660">**Variable environnement**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="cdd89-661">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="cdd89-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="cdd89-662">ShutdownTimeout</span></span>

<span data-ttu-id="cdd89-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="cdd89-664">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="cdd89-664">The default value is five seconds.</span></span>  <span data-ttu-id="cdd89-665">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="cdd89-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="cdd89-666">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="cdd89-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="cdd89-667">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="cdd89-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="cdd89-668">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="cdd89-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="cdd89-669">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="cdd89-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="cdd89-670">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="cdd89-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="cdd89-671">**Clé**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="cdd89-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="cdd89-672">**Type**:`int`</span><span class="sxs-lookup"><span data-stu-id="cdd89-672">**Type**: `int`</span></span>  
<span data-ttu-id="cdd89-673">**Défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="cdd89-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="cdd89-674">**Variable environnement**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="cdd89-675">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="cdd89-676">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="cdd89-677">Recharger la configuration de l’application désactiver sur le changement</span><span class="sxs-lookup"><span data-stu-id="cdd89-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="cdd89-678">Par [défaut,](xref:fundamentals/configuration/index#default) *appsettings.json* et *appsettings. Environnement-json* sont rechargés lorsque le fichier change.</span><span class="sxs-lookup"><span data-stu-id="cdd89-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="cdd89-679">Pour désactiver ce comportement de recharge dans ASP.NET Core 5.0 Aperçu `hostBuilder:reloadConfigOnChange` 3 `false`ou plus tard, définissez la clé de .</span><span class="sxs-lookup"><span data-stu-id="cdd89-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="cdd89-680">**Clé**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="cdd89-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="cdd89-681">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-682">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="cdd89-682">**Default**: `true`</span></span>  
<span data-ttu-id="cdd89-683">**Argument de la ligne de commandement**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="cdd89-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="cdd89-684">**Variable environnement**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="cdd89-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="cdd89-685">Le séparateur du côlon ()`:`ne fonctionne pas avec des touches hiérarchiques variables de l’environnement sur toutes les plates-formes.</span><span class="sxs-lookup"><span data-stu-id="cdd89-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="cdd89-686">Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="cdd89-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="cdd89-687">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="cdd89-687">Settings for web apps</span></span>

<span data-ttu-id="cdd89-688">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="cdd89-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="cdd89-689">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="cdd89-690">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="cdd89-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="cdd89-691">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="cdd89-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="cdd89-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="cdd89-692">CaptureStartupErrors</span></span>

<span data-ttu-id="cdd89-693">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="cdd89-694">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="cdd89-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="cdd89-695">**Clé**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="cdd89-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="cdd89-696">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-697">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="cdd89-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="cdd89-698">**Variable environnement**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="cdd89-699">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="cdd89-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="cdd89-700">DetailedErrors</span></span>

<span data-ttu-id="cdd89-701">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="cdd89-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="cdd89-702">**Clé**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="cdd89-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="cdd89-703">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-704">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="cdd89-704">**Default**: `false`</span></span>  
<span data-ttu-id="cdd89-705">**Variable environnement**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="cdd89-706">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="cdd89-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="cdd89-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="cdd89-708">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="cdd89-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="cdd89-709">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="cdd89-710">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="cdd89-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="cdd89-711">**Clé**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="cdd89-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="cdd89-712">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-712">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-713">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="cdd89-713">**Default**: Empty string</span></span>  
<span data-ttu-id="cdd89-714">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="cdd89-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="cdd89-715">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="cdd89-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="cdd89-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="cdd89-717">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="cdd89-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="cdd89-718">**Clé**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="cdd89-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="cdd89-719">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-719">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-720">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="cdd89-720">**Default**: Empty string</span></span>  
<span data-ttu-id="cdd89-721">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="cdd89-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="cdd89-722">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="cdd89-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="cdd89-723">HTTPS_Port</span></span>

<span data-ttu-id="cdd89-724">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cdd89-724">The HTTPS redirect port.</span></span> <span data-ttu-id="cdd89-725">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="cdd89-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="cdd89-726">**Clé**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="cdd89-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="cdd89-727">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-727">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-728">**Défaut**: Une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="cdd89-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="cdd89-729">**Variable environnement**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="cdd89-730">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="cdd89-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="cdd89-731">PreferHostingUrls</span></span>

<span data-ttu-id="cdd89-732">Indique si l’hôte doit écouter sur les `IWebHostBuilder` URL configurées avec les `IServer` URL configurées au lieu de ces URL configurées avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="cdd89-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="cdd89-733">**Clé**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="cdd89-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="cdd89-734">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-735">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="cdd89-735">**Default**: `true`</span></span>  
<span data-ttu-id="cdd89-736">**Variable environnement**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="cdd89-737">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="cdd89-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="cdd89-738">PreventHostingStartup</span></span>

<span data-ttu-id="cdd89-739">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="cdd89-740">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="cdd89-741">**Clé**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="cdd89-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="cdd89-742">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="cdd89-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="cdd89-743">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="cdd89-743">**Default**: `false`</span></span>  
<span data-ttu-id="cdd89-744">**Variable environnement**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="cdd89-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="cdd89-745">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="cdd89-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="cdd89-746">StartupAssembly</span></span>

<span data-ttu-id="cdd89-747">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="cdd89-748">**Clé**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="cdd89-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="cdd89-749">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-749">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-750">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="cdd89-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="cdd89-751">**Variable environnement**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="cdd89-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="cdd89-752">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="cdd89-753">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="cdd89-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="cdd89-754">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="cdd89-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="cdd89-755">URLs</span><span class="sxs-lookup"><span data-stu-id="cdd89-755">URLs</span></span>

<span data-ttu-id="cdd89-756">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="cdd89-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="cdd89-757">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="cdd89-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="cdd89-758">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="cdd89-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="cdd89-759">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="cdd89-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="cdd89-760">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="cdd89-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="cdd89-761">**Clé**:`urls`</span><span class="sxs-lookup"><span data-stu-id="cdd89-761">**Key**: `urls`</span></span>  
<span data-ttu-id="cdd89-762">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-762">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-763">**Défaut** `http://localhost:5000` : et`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="cdd89-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="cdd89-764">**Variable environnement**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="cdd89-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="cdd89-765">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="cdd89-766">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="cdd89-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="cdd89-767">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="cdd89-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-768">WebRoot</span></span>

<span data-ttu-id="cdd89-769">La propriété [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin relatif vers les actifs statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="cdd89-770">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="cdd89-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="cdd89-771">**Clé**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="cdd89-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="cdd89-772">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="cdd89-772">**Type**: `string`</span></span>  
<span data-ttu-id="cdd89-773">**Défaut**: La `wwwroot`valeur par défaut est .</span><span class="sxs-lookup"><span data-stu-id="cdd89-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="cdd89-774">Le chemin vers *la « racine de contenu »/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="cdd89-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="cdd89-775">**Variable environnement**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="cdd89-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="cdd89-776">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="cdd89-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="cdd89-777">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="cdd89-777">For more information, see:</span></span>

* [<span data-ttu-id="cdd89-778">Fondamentaux : racine web</span><span class="sxs-lookup"><span data-stu-id="cdd89-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="cdd89-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="cdd89-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="cdd89-780">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cdd89-780">Manage the host lifetime</span></span>

<span data-ttu-id="cdd89-781">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="cdd89-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="cdd89-782">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="cdd89-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="cdd89-783">Exécuter</span><span class="sxs-lookup"><span data-stu-id="cdd89-783">Run</span></span>

<span data-ttu-id="cdd89-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="cdd89-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="cdd89-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-785">RunAsync</span></span>

<span data-ttu-id="cdd89-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="cdd89-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="cdd89-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-787">RunConsoleAsync</span></span>

<span data-ttu-id="cdd89-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="cdd89-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="cdd89-789">Démarrer</span><span class="sxs-lookup"><span data-stu-id="cdd89-789">Start</span></span>

<span data-ttu-id="cdd89-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="cdd89-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="cdd89-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-791">StartAsync</span></span>

<span data-ttu-id="cdd89-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="cdd89-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="cdd89-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="cdd89-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="cdd89-794">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="cdd89-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="cdd89-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-795">StopAsync</span></span>

<span data-ttu-id="cdd89-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="cdd89-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="cdd89-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="cdd89-797">WaitForShutdown</span></span>

<span data-ttu-id="cdd89-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le fil d’appel jusqu’à ce que l’arrêt soit déclenché par l’IHostLifetime, par exemple via <kbd>Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="cdd89-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="cdd89-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="cdd89-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="cdd89-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cdd89-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="cdd89-801">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="cdd89-801">External control</span></span>

<span data-ttu-id="cdd89-802">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="cdd89-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="cdd89-803">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cdd89-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
