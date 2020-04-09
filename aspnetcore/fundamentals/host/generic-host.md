---
title: Hôte générique .NET
author: rick-anderson
description: Découvrez l’hôte générique .NET Core, qui est responsable de la gestion du démarrage et de la durée de vie des applications.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 454216cec72048217ede412f8ff6d4261f7353b1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417631"
---
# <a name="net-generic-host"></a><span data-ttu-id="e4573-103">Hôte générique .NET</span><span class="sxs-lookup"><span data-stu-id="e4573-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e4573-104">Cet article présente l’hôte générique .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) et fournit des conseils sur son utilisation.</span><span class="sxs-lookup"><span data-stu-id="e4573-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="e4573-105">Qu’est-ce qu’un hôte ?</span><span class="sxs-lookup"><span data-stu-id="e4573-105">What's a host?</span></span>

<span data-ttu-id="e4573-106">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="e4573-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e4573-107">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="e4573-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="e4573-108">Journalisation</span><span class="sxs-lookup"><span data-stu-id="e4573-108">Logging</span></span>
* <span data-ttu-id="e4573-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="e4573-109">Configuration</span></span>
* <span data-ttu-id="e4573-110">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="e4573-110">`IHostedService` implementations</span></span>

<span data-ttu-id="e4573-111">Quand un hôte démarre, il appelle `IHostedService.StartAsync` sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> qu’il trouve dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e4573-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="e4573-112">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="e4573-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e4573-113">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="e4573-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="e4573-114">Dans les versions d’ASP.NET Core antérieures à 3.0, l’[hôte web](xref:fundamentals/host/web-host) est utilisé pour les charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="e4573-115">L’hôte web n’est plus recommandé pour les applications web, mais reste disponible uniquement à des fins de compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="e4573-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e4573-116">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-116">Set up a host</span></span>

<span data-ttu-id="e4573-117">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="e4573-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e4573-118">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="e4573-118">The `Main` method:</span></span>

* <span data-ttu-id="e4573-119">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="e4573-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e4573-120">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="e4573-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e4573-121">Voici le code de *Program.cs* pour une charge de travail non-HTTP, avec une seule implémentation `IHostedService` ajoutée au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e4573-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="e4573-122">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="e4573-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e4573-123">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e4573-124">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e4573-125">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="e4573-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e4573-126">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="e4573-126">Default builder settings</span></span>

<span data-ttu-id="e4573-127">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="e4573-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="e4573-128">Définit la [racine de](xref:fundamentals/index#content-root) contenu <xref:System.IO.Directory.GetCurrentDirectory*>sur le chemin retourné par .</span><span class="sxs-lookup"><span data-stu-id="e4573-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="e4573-129">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="e4573-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="e4573-130">Variables de l’environnement préfixées avec `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="e4573-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e4573-131">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e4573-131">Command-line arguments.</span></span>
* <span data-ttu-id="e4573-132">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="e4573-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="e4573-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e4573-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="e4573-134">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="e4573-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e4573-135">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="e4573-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e4573-136">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-136">Environment variables.</span></span>
  * <span data-ttu-id="e4573-137">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e4573-137">Command-line arguments.</span></span>
* <span data-ttu-id="e4573-138">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="e4573-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e4573-139">Console</span><span class="sxs-lookup"><span data-stu-id="e4573-139">Console</span></span>
  * <span data-ttu-id="e4573-140">Débogage</span><span class="sxs-lookup"><span data-stu-id="e4573-140">Debug</span></span>
  * <span data-ttu-id="e4573-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="e4573-141">EventSource</span></span>
  * <span data-ttu-id="e4573-142">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="e4573-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e4573-143">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e4573-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e4573-144">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="e4573-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="e4573-145">Charge la configuration de l’hôte `ASPNETCORE_`à partir de variables de l’environnement préfixées avec .</span><span class="sxs-lookup"><span data-stu-id="e4573-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e4573-146">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e4573-147">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="e4573-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="e4573-148">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="e4573-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="e4573-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span><span class="sxs-lookup"><span data-stu-id="e4573-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e4573-150">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="e4573-150">Enables IIS integration.</span></span> <span data-ttu-id="e4573-151">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="e4573-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e4573-152">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e4573-153">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="e4573-153">Framework-provided services</span></span>

<span data-ttu-id="e4573-154">Les services suivants sont enregistrés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="e4573-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e4573-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e4573-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e4573-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e4573-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e4573-158">Pour plus d’informations sur <xref:fundamentals/dependency-injection#framework-provided-services>les services fournis par le cadre, voir .</span><span class="sxs-lookup"><span data-stu-id="e4573-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e4573-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="e4573-160">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="e4573-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e4573-161">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e4573-162">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="e4573-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e4573-163">L’exemple suivant `IHostedService` est une `IHostApplicationLifetime` implémentation qui enregistre les événements :</span><span class="sxs-lookup"><span data-stu-id="e4573-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e4573-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-164">IHostLifetime</span></span>

<span data-ttu-id="e4573-165">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e4573-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e4573-166">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e4573-166">The last implementation registered is used.</span></span>

<span data-ttu-id="e4573-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e4573-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e4573-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e4573-169">Écoutez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="e4573-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="e4573-170">Débloque des extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="e4573-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e4573-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e4573-171">IHostEnvironment</span></span>

<span data-ttu-id="e4573-172">Injecter <xref:Microsoft.Extensions.Hosting.IHostEnvironment> le service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="e4573-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e4573-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e4573-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e4573-174">EnvironmentName (en)</span><span class="sxs-lookup"><span data-stu-id="e4573-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e4573-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e4573-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="e4573-176">Les applications `IWebHostEnvironment` Web implémentent l’interface, qui hérite `IHostEnvironment` et ajoute le [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="e4573-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e4573-177">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-177">Host configuration</span></span>

<span data-ttu-id="e4573-178">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="e4573-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e4573-179">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="e4573-180">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e4573-181">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e4573-182">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e4573-183">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="e4573-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e4573-184">Le fournisseur variable d’environnement avec des arguments de préfixe `DOTNET_` et de commande-ligne sont inclus par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e4573-185">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="e4573-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e4573-186">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e4573-187">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="e4573-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e4573-188">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="e4573-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e4573-189">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="e4573-189">App configuration</span></span>

<span data-ttu-id="e4573-190">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e4573-191">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e4573-192">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="e4573-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e4573-193">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e4573-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e4573-194">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e4573-195">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="e4573-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e4573-196">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="e4573-196">Settings for all app types</span></span>

<span data-ttu-id="e4573-197">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e4573-198">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e4573-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e4573-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e4573-199">ApplicationName</span></span>

<span data-ttu-id="e4573-200">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e4573-201">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="e4573-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e4573-202">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-202">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-203">**Défaut**: Nom de l’assemblage qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e4573-204">**Variable environnement**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e4573-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e4573-205">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="e4573-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e4573-206">ContentRootPath</span></span>

<span data-ttu-id="e4573-207">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="e4573-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e4573-208">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="e4573-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e4573-209">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e4573-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e4573-210">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-210">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-211">**Défaut**: Le dossier où se trouve l’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e4573-212">**Variable environnement**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e4573-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e4573-213">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e4573-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e4573-214">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="e4573-214">For more information, see:</span></span>

* [<span data-ttu-id="e4573-215">Fondamentaux : Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="e4573-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e4573-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e4573-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e4573-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e4573-217">EnvironmentName</span></span>

<span data-ttu-id="e4573-218">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="e4573-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e4573-219">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="e4573-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e4573-220">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="e4573-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e4573-221">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="e4573-221">**Key**: `environment`</span></span>  
<span data-ttu-id="e4573-222">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-222">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-223">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="e4573-223">**Default**: `Production`</span></span>  
<span data-ttu-id="e4573-224">**Variable environnement**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e4573-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e4573-225">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e4573-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e4573-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e4573-226">ShutdownTimeout</span></span>

<span data-ttu-id="e4573-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e4573-228">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="e4573-228">The default value is five seconds.</span></span>  <span data-ttu-id="e4573-229">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="e4573-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="e4573-230">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="e4573-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e4573-231">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="e4573-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e4573-232">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e4573-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e4573-233">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="e4573-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e4573-234">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="e4573-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e4573-235">**Clé**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e4573-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e4573-236">**Type**:`int`</span><span class="sxs-lookup"><span data-stu-id="e4573-236">**Type**: `int`</span></span>  
<span data-ttu-id="e4573-237">**Défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="e4573-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e4573-238">**Variable environnement**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e4573-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e4573-239">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="e4573-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e4573-240">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="e4573-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="e4573-241">Recharger la configuration de l’application désactiver sur le changement</span><span class="sxs-lookup"><span data-stu-id="e4573-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="e4573-242">Par [défaut,](xref:fundamentals/configuration/index#default) *appsettings.json* et *appsettings. Environnement-json* sont rechargés lorsque le fichier change.</span><span class="sxs-lookup"><span data-stu-id="e4573-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="e4573-243">Pour désactiver ce comportement de recharge dans ASP.NET Core 5.0 Aperçu `hostBuilder:reloadConfigOnChange` 3 `false`ou plus tard, définissez la clé de .</span><span class="sxs-lookup"><span data-stu-id="e4573-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="e4573-244">**Clé**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e4573-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e4573-245">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-246">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="e4573-246">**Default**: `true`</span></span>  
<span data-ttu-id="e4573-247">**Argument de la ligne de commandement**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e4573-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e4573-248">**Variable environnement**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e4573-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="e4573-249">Le séparateur du côlon ()`:`ne fonctionne pas avec des touches hiérarchiques variables de l’environnement sur toutes les plates-formes.</span><span class="sxs-lookup"><span data-stu-id="e4573-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="e4573-250">Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e4573-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="e4573-251">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="e4573-251">Settings for web apps</span></span>

<span data-ttu-id="e4573-252">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e4573-253">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e4573-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e4573-254">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="e4573-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e4573-255">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e4573-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e4573-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e4573-256">CaptureStartupErrors</span></span>

<span data-ttu-id="e4573-257">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e4573-258">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="e4573-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e4573-259">**Clé**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e4573-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e4573-260">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-261">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="e4573-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e4573-262">**Variable environnement**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e4573-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e4573-263">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="e4573-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e4573-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e4573-264">DetailedErrors</span></span>

<span data-ttu-id="e4573-265">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="e4573-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e4573-266">**Clé**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e4573-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e4573-267">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-268">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="e4573-268">**Default**: `false`</span></span>  
<span data-ttu-id="e4573-269">**Variable environnement**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e4573-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e4573-270">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e4573-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e4573-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="e4573-272">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e4573-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e4573-273">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e4573-274">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e4573-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e4573-275">**Clé**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e4573-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e4573-276">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-276">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-277">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="e4573-277">**Default**: Empty string</span></span>  
<span data-ttu-id="e4573-278">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e4573-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e4573-279">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e4573-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e4573-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e4573-281">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e4573-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e4573-282">**Clé**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e4573-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e4573-283">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-283">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-284">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="e4573-284">**Default**: Empty string</span></span>  
<span data-ttu-id="e4573-285">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e4573-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e4573-286">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e4573-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e4573-287">HTTPS_Port</span></span>

<span data-ttu-id="e4573-288">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e4573-288">The HTTPS redirect port.</span></span> <span data-ttu-id="e4573-289">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e4573-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e4573-290">**Clé**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="e4573-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="e4573-291">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-291">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-292">**Défaut**: Une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="e4573-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e4573-293">**Variable environnement**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e4573-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e4573-294">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e4573-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="e4573-295">PreferHostingUrls</span></span>

<span data-ttu-id="e4573-296">Indique si l’hôte doit écouter sur les `IWebHostBuilder` URL configurées avec les `IServer` URL configurées au lieu de ces URL configurées avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="e4573-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e4573-297">**Clé**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e4573-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e4573-298">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-299">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="e4573-299">**Default**: `true`</span></span>  
<span data-ttu-id="e4573-300">**Variable environnement**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e4573-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e4573-301">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="e4573-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e4573-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="e4573-302">PreventHostingStartup</span></span>

<span data-ttu-id="e4573-303">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e4573-304">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e4573-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e4573-305">**Clé**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e4573-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e4573-306">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-307">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="e4573-307">**Default**: `false`</span></span>  
<span data-ttu-id="e4573-308">**Variable environnement**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e4573-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e4573-309">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e4573-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e4573-310">StartupAssembly</span></span>

<span data-ttu-id="e4573-311">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e4573-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e4573-312">**Clé**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e4573-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e4573-313">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-313">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-314">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="e4573-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e4573-315">**Variable environnement**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e4573-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e4573-316">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="e4573-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e4573-317">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="e4573-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e4573-318">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e4573-319">URLs</span><span class="sxs-lookup"><span data-stu-id="e4573-319">URLs</span></span>

<span data-ttu-id="e4573-320">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="e4573-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e4573-321">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="e4573-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e4573-322">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="e4573-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e4573-323">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="e4573-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e4573-324">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="e4573-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="e4573-325">**Clé**:`urls`</span><span class="sxs-lookup"><span data-stu-id="e4573-325">**Key**: `urls`</span></span>  
<span data-ttu-id="e4573-326">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-326">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-327">**Défaut** `http://localhost:5000` : et`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e4573-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e4573-328">**Variable environnement**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e4573-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e4573-329">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="e4573-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e4573-330">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e4573-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e4573-331">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e4573-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e4573-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e4573-332">WebRoot</span></span>

<span data-ttu-id="e4573-333">Chemin relatif des ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="e4573-334">**Clé**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="e4573-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="e4573-335">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-335">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-336">**Défaut**: La `wwwroot`valeur par défaut est .</span><span class="sxs-lookup"><span data-stu-id="e4573-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e4573-337">Le chemin vers *la « racine de contenu »/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="e4573-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="e4573-338">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="e4573-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="e4573-339">**Variable environnement**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e4573-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e4573-340">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` :</span><span class="sxs-lookup"><span data-stu-id="e4573-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e4573-341">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="e4573-341">For more information, see:</span></span>

* [<span data-ttu-id="e4573-342">Fondamentaux : racine web</span><span class="sxs-lookup"><span data-stu-id="e4573-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e4573-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e4573-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e4573-344">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-344">Manage the host lifetime</span></span>

<span data-ttu-id="e4573-345">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e4573-346">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e4573-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e4573-347">Exécuter</span><span class="sxs-lookup"><span data-stu-id="e4573-347">Run</span></span>

<span data-ttu-id="e4573-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e4573-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-349">RunAsync</span></span>

<span data-ttu-id="e4573-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="e4573-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e4573-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-351">RunConsoleAsync</span></span>

<span data-ttu-id="e4573-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e4573-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e4573-353">Démarrer</span><span class="sxs-lookup"><span data-stu-id="e4573-353">Start</span></span>

<span data-ttu-id="e4573-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="e4573-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e4573-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-355">StartAsync</span></span>

<span data-ttu-id="e4573-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="e4573-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e4573-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="e4573-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e4573-358">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="e4573-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e4573-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-359">StopAsync</span></span>

<span data-ttu-id="e4573-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="e4573-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e4573-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e4573-361">WaitForShutdown</span></span>

<span data-ttu-id="e4573-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le fil d’appel jusqu’à ce que l’arrêt soit déclenché par l’IHostLifetime, par exemple via <kbd>Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="e4573-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e4573-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="e4573-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e4573-365">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="e4573-365">External control</span></span>

<span data-ttu-id="e4573-366">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="e4573-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="e4573-367">Cet article présente l’hôte générique .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) et fournit des conseils sur son utilisation.</span><span class="sxs-lookup"><span data-stu-id="e4573-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="e4573-368">Qu’est-ce qu’un hôte ?</span><span class="sxs-lookup"><span data-stu-id="e4573-368">What's a host?</span></span>

<span data-ttu-id="e4573-369">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="e4573-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e4573-370">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="e4573-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="e4573-371">Journalisation</span><span class="sxs-lookup"><span data-stu-id="e4573-371">Logging</span></span>
* <span data-ttu-id="e4573-372">Configuration</span><span class="sxs-lookup"><span data-stu-id="e4573-372">Configuration</span></span>
* <span data-ttu-id="e4573-373">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="e4573-373">`IHostedService` implementations</span></span>

<span data-ttu-id="e4573-374">Quand un hôte démarre, il appelle `IHostedService.StartAsync` sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> qu’il trouve dans le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e4573-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="e4573-375">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="e4573-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e4573-376">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="e4573-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="e4573-377">Dans les versions d’ASP.NET Core antérieures à 3.0, l’[hôte web](xref:fundamentals/host/web-host) est utilisé pour les charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="e4573-378">L’hôte web n’est plus recommandé pour les applications web, mais reste disponible uniquement à des fins de compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="e4573-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e4573-379">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-379">Set up a host</span></span>

<span data-ttu-id="e4573-380">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="e4573-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e4573-381">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="e4573-381">The `Main` method:</span></span>

* <span data-ttu-id="e4573-382">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="e4573-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e4573-383">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="e4573-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e4573-384">Voici le code de *Program.cs* pour une charge de travail non-HTTP, avec une seule implémentation `IHostedService` ajoutée au conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e4573-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="e4573-385">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="e4573-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e4573-386">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e4573-387">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e4573-388">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="e4573-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e4573-389">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="e4573-389">Default builder settings</span></span>

<span data-ttu-id="e4573-390">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="e4573-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="e4573-391">Définit la [racine de](xref:fundamentals/index#content-root) contenu <xref:System.IO.Directory.GetCurrentDirectory*>sur le chemin retourné par .</span><span class="sxs-lookup"><span data-stu-id="e4573-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="e4573-392">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="e4573-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="e4573-393">Variables de l’environnement préfixées avec `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="e4573-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e4573-394">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e4573-394">Command-line arguments.</span></span>
* <span data-ttu-id="e4573-395">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="e4573-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="e4573-396">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e4573-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="e4573-397">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="e4573-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e4573-398">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="e4573-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e4573-399">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-399">Environment variables.</span></span>
  * <span data-ttu-id="e4573-400">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="e4573-400">Command-line arguments.</span></span>
* <span data-ttu-id="e4573-401">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="e4573-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e4573-402">Console</span><span class="sxs-lookup"><span data-stu-id="e4573-402">Console</span></span>
  * <span data-ttu-id="e4573-403">Débogage</span><span class="sxs-lookup"><span data-stu-id="e4573-403">Debug</span></span>
  * <span data-ttu-id="e4573-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="e4573-404">EventSource</span></span>
  * <span data-ttu-id="e4573-405">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="e4573-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e4573-406">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e4573-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e4573-407">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="e4573-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="e4573-408">Charge la configuration de l’hôte `ASPNETCORE_`à partir de variables de l’environnement préfixées avec .</span><span class="sxs-lookup"><span data-stu-id="e4573-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e4573-409">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e4573-410">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="e4573-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="e4573-411">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="e4573-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="e4573-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span><span class="sxs-lookup"><span data-stu-id="e4573-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e4573-413">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="e4573-413">Enables IIS integration.</span></span> <span data-ttu-id="e4573-414">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="e4573-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e4573-415">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e4573-416">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="e4573-416">Framework-provided services</span></span>

<span data-ttu-id="e4573-417">Les services suivants sont enregistrés automatiquement :</span><span class="sxs-lookup"><span data-stu-id="e4573-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e4573-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e4573-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e4573-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e4573-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e4573-421">Pour plus d’informations sur <xref:fundamentals/dependency-injection#framework-provided-services>les services fournis par le cadre, voir .</span><span class="sxs-lookup"><span data-stu-id="e4573-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e4573-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="e4573-423">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="e4573-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e4573-424">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e4573-425">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="e4573-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e4573-426">L’exemple suivant `IHostedService` est une `IHostApplicationLifetime` implémentation qui enregistre les événements :</span><span class="sxs-lookup"><span data-stu-id="e4573-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e4573-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-427">IHostLifetime</span></span>

<span data-ttu-id="e4573-428">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e4573-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e4573-429">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e4573-429">The last implementation registered is used.</span></span>

<span data-ttu-id="e4573-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e4573-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e4573-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e4573-432">Écoutez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="e4573-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="e4573-433">Débloque des extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="e4573-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e4573-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e4573-434">IHostEnvironment</span></span>

<span data-ttu-id="e4573-435">Injecter <xref:Microsoft.Extensions.Hosting.IHostEnvironment> le service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="e4573-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e4573-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e4573-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e4573-437">EnvironmentName (en)</span><span class="sxs-lookup"><span data-stu-id="e4573-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e4573-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e4573-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="e4573-439">Les applications `IWebHostEnvironment` Web implémentent l’interface, qui hérite `IHostEnvironment` et ajoute le [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="e4573-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e4573-440">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-440">Host configuration</span></span>

<span data-ttu-id="e4573-441">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="e4573-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e4573-442">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="e4573-443">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e4573-444">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e4573-445">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e4573-446">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="e4573-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e4573-447">Le fournisseur variable d’environnement avec des arguments de préfixe `DOTNET_` et de commande-ligne sont inclus par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e4573-448">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="e4573-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e4573-449">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e4573-450">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="e4573-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e4573-451">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="e4573-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e4573-452">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="e4573-452">App configuration</span></span>

<span data-ttu-id="e4573-453">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e4573-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e4573-454">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e4573-455">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="e4573-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e4573-456">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="e4573-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e4573-457">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e4573-458">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="e4573-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e4573-459">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="e4573-459">Settings for all app types</span></span>

<span data-ttu-id="e4573-460">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e4573-461">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e4573-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e4573-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e4573-462">ApplicationName</span></span>

<span data-ttu-id="e4573-463">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e4573-464">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="e4573-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e4573-465">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-465">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-466">**Défaut**: Nom de l’assemblage qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e4573-467">**Variable environnement**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e4573-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e4573-468">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="e4573-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e4573-469">ContentRootPath</span></span>

<span data-ttu-id="e4573-470">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="e4573-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e4573-471">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="e4573-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e4573-472">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e4573-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e4573-473">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-473">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-474">**Défaut**: Le dossier où se trouve l’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e4573-475">**Variable environnement**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e4573-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e4573-476">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e4573-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e4573-477">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="e4573-477">For more information, see:</span></span>

* [<span data-ttu-id="e4573-478">Fondamentaux : Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="e4573-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e4573-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e4573-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e4573-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e4573-480">EnvironmentName</span></span>

<span data-ttu-id="e4573-481">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="e4573-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e4573-482">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="e4573-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e4573-483">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="e4573-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e4573-484">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="e4573-484">**Key**: `environment`</span></span>  
<span data-ttu-id="e4573-485">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-485">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-486">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="e4573-486">**Default**: `Production`</span></span>  
<span data-ttu-id="e4573-487">**Variable environnement**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e4573-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e4573-488">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e4573-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e4573-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e4573-489">ShutdownTimeout</span></span>

<span data-ttu-id="e4573-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e4573-491">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="e4573-491">The default value is five seconds.</span></span>  <span data-ttu-id="e4573-492">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="e4573-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="e4573-493">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="e4573-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e4573-494">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="e4573-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e4573-495">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e4573-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e4573-496">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="e4573-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e4573-497">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="e4573-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e4573-498">**Clé**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e4573-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e4573-499">**Type**:`int`</span><span class="sxs-lookup"><span data-stu-id="e4573-499">**Type**: `int`</span></span>  
<span data-ttu-id="e4573-500">**Défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="e4573-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e4573-501">**Variable environnement**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e4573-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e4573-502">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="e4573-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e4573-503">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="e4573-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="e4573-504">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="e4573-504">Settings for web apps</span></span>

<span data-ttu-id="e4573-505">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e4573-506">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e4573-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e4573-507">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="e4573-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e4573-508">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e4573-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e4573-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e4573-509">CaptureStartupErrors</span></span>

<span data-ttu-id="e4573-510">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e4573-511">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="e4573-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e4573-512">**Clé**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e4573-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e4573-513">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-514">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="e4573-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e4573-515">**Variable environnement**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e4573-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e4573-516">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="e4573-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e4573-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e4573-517">DetailedErrors</span></span>

<span data-ttu-id="e4573-518">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="e4573-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e4573-519">**Clé**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e4573-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e4573-520">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-521">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="e4573-521">**Default**: `false`</span></span>  
<span data-ttu-id="e4573-522">**Variable environnement**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e4573-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e4573-523">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e4573-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e4573-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="e4573-525">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e4573-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e4573-526">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e4573-527">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e4573-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e4573-528">**Clé**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e4573-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e4573-529">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-529">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-530">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="e4573-530">**Default**: Empty string</span></span>  
<span data-ttu-id="e4573-531">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e4573-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e4573-532">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e4573-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e4573-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e4573-534">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="e4573-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e4573-535">**Clé**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e4573-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e4573-536">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-536">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-537">**Défaut**: Chaîne vide</span><span class="sxs-lookup"><span data-stu-id="e4573-537">**Default**: Empty string</span></span>  
<span data-ttu-id="e4573-538">**Variable environnement**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e4573-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e4573-539">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e4573-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e4573-540">HTTPS_Port</span></span>

<span data-ttu-id="e4573-541">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e4573-541">The HTTPS redirect port.</span></span> <span data-ttu-id="e4573-542">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e4573-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e4573-543">**Clé**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="e4573-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="e4573-544">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-544">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-545">**Défaut**: Une valeur par défaut n’est pas définie.</span><span class="sxs-lookup"><span data-stu-id="e4573-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e4573-546">**Variable environnement**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e4573-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e4573-547">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e4573-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="e4573-548">PreferHostingUrls</span></span>

<span data-ttu-id="e4573-549">Indique si l’hôte doit écouter sur les `IWebHostBuilder` URL configurées avec les `IServer` URL configurées au lieu de ces URL configurées avec l’implémentation.</span><span class="sxs-lookup"><span data-stu-id="e4573-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e4573-550">**Clé**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e4573-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e4573-551">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-552">**Défaut**:`true`</span><span class="sxs-lookup"><span data-stu-id="e4573-552">**Default**: `true`</span></span>  
<span data-ttu-id="e4573-553">**Variable environnement**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e4573-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e4573-554">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="e4573-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e4573-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="e4573-555">PreventHostingStartup</span></span>

<span data-ttu-id="e4573-556">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e4573-557">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e4573-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e4573-558">**Clé**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e4573-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e4573-559">**Type** `bool` :`true` `1`( ou )</span><span class="sxs-lookup"><span data-stu-id="e4573-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e4573-560">**Défaut**:`false`</span><span class="sxs-lookup"><span data-stu-id="e4573-560">**Default**: `false`</span></span>  
<span data-ttu-id="e4573-561">**Variable environnement**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e4573-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e4573-562">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="e4573-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e4573-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e4573-563">StartupAssembly</span></span>

<span data-ttu-id="e4573-564">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e4573-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e4573-565">**Clé**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e4573-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e4573-566">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-566">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-567">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="e4573-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e4573-568">**Variable environnement**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e4573-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e4573-569">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="e4573-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e4573-570">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="e4573-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e4573-571">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e4573-572">URLs</span><span class="sxs-lookup"><span data-stu-id="e4573-572">URLs</span></span>

<span data-ttu-id="e4573-573">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="e4573-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e4573-574">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="e4573-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e4573-575">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="e4573-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e4573-576">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="e4573-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e4573-577">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="e4573-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="e4573-578">**Clé**:`urls`</span><span class="sxs-lookup"><span data-stu-id="e4573-578">**Key**: `urls`</span></span>  
<span data-ttu-id="e4573-579">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-579">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-580">**Défaut** `http://localhost:5000` : et`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e4573-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e4573-581">**Variable environnement**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e4573-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e4573-582">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="e4573-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e4573-583">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="e4573-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e4573-584">Pour plus d’informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e4573-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e4573-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e4573-585">WebRoot</span></span>

<span data-ttu-id="e4573-586">Chemin relatif des ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="e4573-587">**Clé**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="e4573-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="e4573-588">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-588">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-589">**Défaut**: La `wwwroot`valeur par défaut est .</span><span class="sxs-lookup"><span data-stu-id="e4573-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e4573-590">Le chemin vers *la « racine de contenu »/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="e4573-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="e4573-591">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="e4573-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="e4573-592">**Variable environnement**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e4573-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e4573-593">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` :</span><span class="sxs-lookup"><span data-stu-id="e4573-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e4573-594">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="e4573-594">For more information, see:</span></span>

* [<span data-ttu-id="e4573-595">Fondamentaux : racine web</span><span class="sxs-lookup"><span data-stu-id="e4573-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e4573-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e4573-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e4573-597">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-597">Manage the host lifetime</span></span>

<span data-ttu-id="e4573-598">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e4573-599">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e4573-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e4573-600">Exécuter</span><span class="sxs-lookup"><span data-stu-id="e4573-600">Run</span></span>

<span data-ttu-id="e4573-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e4573-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-602">RunAsync</span></span>

<span data-ttu-id="e4573-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="e4573-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e4573-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-604">RunConsoleAsync</span></span>

<span data-ttu-id="e4573-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e4573-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e4573-606">Démarrer</span><span class="sxs-lookup"><span data-stu-id="e4573-606">Start</span></span>

<span data-ttu-id="e4573-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="e4573-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e4573-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-608">StartAsync</span></span>

<span data-ttu-id="e4573-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="e4573-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e4573-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="e4573-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e4573-611">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="e4573-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e4573-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-612">StopAsync</span></span>

<span data-ttu-id="e4573-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="e4573-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e4573-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e4573-614">WaitForShutdown</span></span>

<span data-ttu-id="e4573-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le fil d’appel jusqu’à ce que l’arrêt soit déclenché par l’IHostLifetime, par exemple via <kbd>Ctrl</kbd>+<kbd>C/SIGINT</kbd>ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="e4573-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e4573-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="e4573-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e4573-618">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="e4573-618">External control</span></span>

<span data-ttu-id="e4573-619">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="e4573-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="e4573-620">Les applications ASP.NET Core configurent et lancent un hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="e4573-621">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="e4573-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="e4573-622">Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="e4573-623">L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="e4573-624">La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="e4573-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="e4573-625">L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web.</span><span class="sxs-lookup"><span data-stu-id="e4573-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="e4573-626">Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="e4573-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="e4573-627">L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="e4573-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="e4573-628">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4573-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e4573-629">Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré*.</span><span class="sxs-lookup"><span data-stu-id="e4573-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="e4573-630">N’exécutez pas l’exemple dans une `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="e4573-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="e4573-631">Pour définir la console dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="e4573-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="e4573-632">Ouvrez le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="e4573-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="e4573-633">Dans la configuration **.NET Core Launch (console)**, recherchez l’entrée **console**.</span><span class="sxs-lookup"><span data-stu-id="e4573-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="e4573-634">Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="e4573-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="e4573-635">Introduction</span><span class="sxs-lookup"><span data-stu-id="e4573-635">Introduction</span></span>

<span data-ttu-id="e4573-636">La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="e4573-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="e4573-637">Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).</span><span class="sxs-lookup"><span data-stu-id="e4573-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="e4573-638"><xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code.</span><span class="sxs-lookup"><span data-stu-id="e4573-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="e4573-639">Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="e4573-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="e4573-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.</span><span class="sxs-lookup"><span data-stu-id="e4573-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e4573-641">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-641">Set up a host</span></span>

<span data-ttu-id="e4573-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :</span><span class="sxs-lookup"><span data-stu-id="e4573-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="e4573-643">Options</span><span class="sxs-lookup"><span data-stu-id="e4573-643">Options</span></span>

<span data-ttu-id="e4573-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="e4573-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="e4573-645">Délai d’expiration</span><span class="sxs-lookup"><span data-stu-id="e4573-645">Shutdown timeout</span></span>

<span data-ttu-id="e4573-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e4573-647">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="e4573-647">The default value is five seconds.</span></span>

<span data-ttu-id="e4573-648">La configuration d’option suivante augmente `Program.Main` le délai d’arrêt par défaut de cinq secondes à 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="e4573-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="e4573-649">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="e4573-649">Default services</span></span>

<span data-ttu-id="e4573-650">Les services suivants sont inscrits au moment de l’initialisation de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="e4573-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="e4573-651">[Environnement](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="e4573-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="e4573-652">[Configuration](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )</span><span class="sxs-lookup"><span data-stu-id="e4573-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="e4573-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e4573-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="e4573-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e4573-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="e4573-655">[Options](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="e4573-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="e4573-656">[Enregistrement](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="e4573-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e4573-657">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-657">Host configuration</span></span>

<span data-ttu-id="e4573-658">La création de la configuration d’hôte fait appel aux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="e4573-658">Host configuration is created by:</span></span>

* <span data-ttu-id="e4573-659">Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="e4573-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="e4573-660">Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="e4573-661">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="e4573-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="e4573-662">Clé d’application (nom)</span><span class="sxs-lookup"><span data-stu-id="e4573-662">Application key (name)</span></span>

<span data-ttu-id="e4573-663">La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="e4573-664">Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :</span><span class="sxs-lookup"><span data-stu-id="e4573-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="e4573-665">**Clé**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="e4573-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e4573-666">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-666">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-667">**Par défaut** : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="e4573-668">**Ensemble à l’aide**de :`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="e4573-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="e4573-669">**Variable de l’environnement**: `<PREFIX_>APPLICATIONNAME` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e4573-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="e4573-670">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="e4573-670">Content root</span></span>

<span data-ttu-id="e4573-671">Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="e4573-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="e4573-672">**Clé**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e4573-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e4573-673">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-673">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-674">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="e4573-675">**Ensemble à l’aide**de :`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="e4573-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="e4573-676">**Variable de l’environnement**: `<PREFIX_>CONTENTROOT` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e4573-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e4573-677">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="e4573-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="e4573-678">Pour plus d’informations, voir [Fundamentals: Content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="e4573-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="e4573-679">Environnement</span><span class="sxs-lookup"><span data-stu-id="e4573-679">Environment</span></span>

<span data-ttu-id="e4573-680">Définit [l’environnement](xref:fundamentals/environments)de l’application .</span><span class="sxs-lookup"><span data-stu-id="e4573-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e4573-681">**Clé**:`environment`</span><span class="sxs-lookup"><span data-stu-id="e4573-681">**Key**: `environment`</span></span>  
<span data-ttu-id="e4573-682">**Type**:`string`</span><span class="sxs-lookup"><span data-stu-id="e4573-682">**Type**: `string`</span></span>  
<span data-ttu-id="e4573-683">**Défaut**:`Production`</span><span class="sxs-lookup"><span data-stu-id="e4573-683">**Default**: `Production`</span></span>  
<span data-ttu-id="e4573-684">**Ensemble à l’aide**de :`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="e4573-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="e4573-685">**Variable de l’environnement**: `<PREFIX_>ENVIRONMENT` (est`<PREFIX_>` [facultative et définie par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e4573-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e4573-686">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="e4573-686">The environment can be set to any value.</span></span> <span data-ttu-id="e4573-687">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="e4573-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e4573-688">Les valeurs ne sont pas sensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="e4573-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="e4573-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="e4573-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="e4573-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="e4573-691">La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="e4573-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e4573-693">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="e4573-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e4573-694">Aucun fournisseur n’est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-694">No providers are included by default.</span></span> <span data-ttu-id="e4573-695">Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :</span><span class="sxs-lookup"><span data-stu-id="e4573-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="e4573-696">Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="e4573-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="e4573-697">Configuration de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-697">Environment variable configuration.</span></span>
* <span data-ttu-id="e4573-698">Configuration de l’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="e4573-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="e4573-699">Tout autre fournisseur de configuration obligatoire.</span><span class="sxs-lookup"><span data-stu-id="e4573-699">Any other required configuration providers.</span></span>

<span data-ttu-id="e4573-700">Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e4573-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="e4573-701">L’exemple d’application utilise un fichier JSON, *hostsettings.json*, et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.</span><span class="sxs-lookup"><span data-stu-id="e4573-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="e4573-702">Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte.</span><span class="sxs-lookup"><span data-stu-id="e4573-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="e4573-703">`AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e4573-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="e4573-704">L’exemple d’application utilise un préfixe `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="e4573-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="e4573-705">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e4573-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e4573-706">Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="e4573-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e4573-707">Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e4573-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="e4573-708">Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="e4573-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="e4573-709">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e4573-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e4573-710">Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="e4573-711">La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.</span><span class="sxs-lookup"><span data-stu-id="e4573-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="e4573-712">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="e4573-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="e4573-713">Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="e4573-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="e4573-714">Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="e4573-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="e4573-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="e4573-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="e4573-716">Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e4573-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="e4573-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="e4573-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e4573-719">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="e4573-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="e4573-720">La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="e4573-721">La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="e4573-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="e4573-722">Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="e4573-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="e4573-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e4573-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="e4573-724">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="e4573-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="e4573-725">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="e4573-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="e4573-726">Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="e4573-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="e4573-727">L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :</span><span class="sxs-lookup"><span data-stu-id="e4573-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="e4573-728">Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="e4573-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="e4573-729">Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="e4573-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="e4573-730">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e4573-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="e4573-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="e4573-731">ConfigureServices</span></span>

<span data-ttu-id="e4573-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e4573-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e4573-734">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="e4573-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e4573-735">Pour plus d’informations, consultez <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="e4573-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="e4573-736">L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :</span><span class="sxs-lookup"><span data-stu-id="e4573-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="e4573-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="e4573-737">ConfigureLogging</span></span>

<span data-ttu-id="e4573-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni.</span><span class="sxs-lookup"><span data-stu-id="e4573-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="e4573-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="e4573-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-740">UseConsoleLifetime</span></span>

<span data-ttu-id="e4573-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour commencer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="e4573-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="e4573-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="e4573-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="e4573-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4573-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="e4573-744">La dernière durée de vie inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="e4573-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="e4573-745">Configuration du conteneur</span><span class="sxs-lookup"><span data-stu-id="e4573-745">Container configuration</span></span>

<span data-ttu-id="e4573-746">Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="e4573-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="e4573-747">L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret.</span><span class="sxs-lookup"><span data-stu-id="e4573-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="e4573-748">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="e4573-749">La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="e4573-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="e4573-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="e4573-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="e4573-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e4573-752">Créer un conteneur de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="e4573-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="e4573-753">Fournir une fabrique de conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="e4573-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="e4573-754">Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :</span><span class="sxs-lookup"><span data-stu-id="e4573-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="e4573-755">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="e4573-755">Extensibility</span></span>

<span data-ttu-id="e4573-756">L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e4573-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="e4573-757">L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="e4573-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="e4573-758">Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :</span><span class="sxs-lookup"><span data-stu-id="e4573-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="e4573-759">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="e4573-759">Manage the host</span></span>

<span data-ttu-id="e4573-760">L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="e4573-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e4573-761">Exécuter</span><span class="sxs-lookup"><span data-stu-id="e4573-761">Run</span></span>

<span data-ttu-id="e4573-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="e4573-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="e4573-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-763">RunAsync</span></span>

<span data-ttu-id="e4573-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :</span><span class="sxs-lookup"><span data-stu-id="e4573-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="e4573-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-765">RunConsoleAsync</span></span>

<span data-ttu-id="e4573-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>permet au support de la console, construit et démarre l’hôte, et attend <kbd>que Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="e4573-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="e4573-767">Start et StopAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-767">Start and StopAsync</span></span>

<span data-ttu-id="e4573-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="e4573-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="e4573-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="e4573-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="e4573-770">StartAsync et StopAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="e4573-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="e4573-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="e4573-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e4573-773">WaitForShutdown</span></span>

<span data-ttu-id="e4573-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché <xref:Microsoft.Extensions.Hosting.IHostLifetime>via le `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , comme (écoute pour <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="e4573-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="e4573-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="e4573-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e4573-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="e4573-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e4573-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="e4573-778">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="e4573-778">External control</span></span>

<span data-ttu-id="e4573-779">Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="e4573-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="e4573-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="e4573-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e4573-781">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="e4573-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="e4573-782">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="e4573-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="e4573-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="e4573-784">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="e4573-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="e4573-785">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e4573-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="e4573-786">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e4573-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="e4573-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="e4573-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="e4573-788">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="e4573-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="e4573-789">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="e4573-789">Cancellation Token</span></span> | <span data-ttu-id="e4573-790">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="e4573-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="e4573-791">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="e4573-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="e4573-792">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="e4573-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="e4573-793">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="e4573-793">All requests should be processed.</span></span> <span data-ttu-id="e4573-794">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="e4573-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="e4573-795">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="e4573-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="e4573-796">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="e4573-796">Requests may still be processing.</span></span> <span data-ttu-id="e4573-797">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="e4573-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="e4573-798">Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe.</span><span class="sxs-lookup"><span data-stu-id="e4573-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="e4573-799">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.</span><span class="sxs-lookup"><span data-stu-id="e4573-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="e4573-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="e4573-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="e4573-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="e4573-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="e4573-802">La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="e4573-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e4573-803">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e4573-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
