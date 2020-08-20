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
ms.openlocfilehash: e606812c1c2164a7e4d0926a76d2ff7ada4c9a87
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635383"
---
# <a name="net-generic-host"></a><span data-ttu-id="db7bd-103">Hôte générique .NET</span><span class="sxs-lookup"><span data-stu-id="db7bd-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="db7bd-104">Les modèles ASP.NET Core créent un hôte générique .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="db7bd-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="db7bd-105">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-105">Host definition</span></span>

<span data-ttu-id="db7bd-106">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="db7bd-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="db7bd-107">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="db7bd-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="db7bd-108">Journalisation</span><span class="sxs-lookup"><span data-stu-id="db7bd-108">Logging</span></span>
* <span data-ttu-id="db7bd-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="db7bd-109">Configuration</span></span>
* <span data-ttu-id="db7bd-110">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="db7bd-110">`IHostedService` implementations</span></span>

<span data-ttu-id="db7bd-111">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="db7bd-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="db7bd-112">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="db7bd-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="db7bd-113">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="db7bd-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="db7bd-114">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-114">Set up a host</span></span>

<span data-ttu-id="db7bd-115">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="db7bd-116">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-116">The `Main` method:</span></span>

* <span data-ttu-id="db7bd-117">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="db7bd-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="db7bd-118">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="db7bd-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="db7bd-119">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte générique :</span><span class="sxs-lookup"><span data-stu-id="db7bd-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="db7bd-120">Le code suivant crée un hôte générique à l’aide d’une charge de travail non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="db7bd-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="db7bd-121">L' `IHostedService` implémentation est ajoutée au conteneur di :</span><span class="sxs-lookup"><span data-stu-id="db7bd-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="db7bd-122">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="db7bd-123">Le code précédent est généré par les modèles ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db7bd-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="db7bd-124">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="db7bd-125">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="db7bd-126">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="db7bd-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="db7bd-127">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="db7bd-127">Default builder settings</span></span>

<span data-ttu-id="db7bd-128">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="db7bd-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="db7bd-129">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="db7bd-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="db7bd-130">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="db7bd-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="db7bd-131">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="db7bd-132">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db7bd-132">Command-line arguments.</span></span>
* <span data-ttu-id="db7bd-133">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="db7bd-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="db7bd-134">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="db7bd-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="db7bd-135">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="db7bd-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="db7bd-136">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="db7bd-137">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-137">Environment variables.</span></span>
  * <span data-ttu-id="db7bd-138">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db7bd-138">Command-line arguments.</span></span>
* <span data-ttu-id="db7bd-139">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="db7bd-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="db7bd-140">Console</span><span class="sxs-lookup"><span data-stu-id="db7bd-140">Console</span></span>
  * <span data-ttu-id="db7bd-141">Débogage</span><span class="sxs-lookup"><span data-stu-id="db7bd-141">Debug</span></span>
  * <span data-ttu-id="db7bd-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="db7bd-142">EventSource</span></span>
  * <span data-ttu-id="db7bd-143">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="db7bd-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="db7bd-144">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="db7bd-145">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="db7bd-146">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="db7bd-147">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="db7bd-148">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="db7bd-149">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="db7bd-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="db7bd-150">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="db7bd-151">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="db7bd-151">Enables IIS integration.</span></span> <span data-ttu-id="db7bd-152">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="db7bd-153">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="db7bd-154">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="db7bd-154">Framework-provided services</span></span>

<span data-ttu-id="db7bd-155">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="db7bd-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="db7bd-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="db7bd-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="db7bd-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="db7bd-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="db7bd-159">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="db7bd-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="db7bd-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="db7bd-161">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="db7bd-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="db7bd-162">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="db7bd-163">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="db7bd-164">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="db7bd-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="db7bd-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-165">IHostLifetime</span></span>

<span data-ttu-id="db7bd-166">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="db7bd-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="db7bd-167">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-167">The last implementation registered is used.</span></span>

<span data-ttu-id="db7bd-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="db7bd-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="db7bd-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="db7bd-170">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="db7bd-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="db7bd-171">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="db7bd-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="db7bd-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="db7bd-172">IHostEnvironment</span></span>

<span data-ttu-id="db7bd-173">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="db7bd-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="db7bd-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="db7bd-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="db7bd-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="db7bd-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="db7bd-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="db7bd-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="db7bd-177">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="db7bd-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="db7bd-178">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-178">Host configuration</span></span>

<span data-ttu-id="db7bd-179">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="db7bd-180">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="db7bd-181">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="db7bd-182">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="db7bd-183">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="db7bd-184">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="db7bd-185">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="db7bd-186">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="db7bd-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="db7bd-187">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="db7bd-188">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="db7bd-189">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="db7bd-190">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="db7bd-190">App configuration</span></span>

<span data-ttu-id="db7bd-191">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="db7bd-192">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="db7bd-193">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="db7bd-194">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="db7bd-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="db7bd-195">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="db7bd-196">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="db7bd-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="db7bd-197">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="db7bd-197">Settings for all app types</span></span>

<span data-ttu-id="db7bd-198">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="db7bd-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="db7bd-199">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="db7bd-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="db7bd-200">ApplicationName</span></span>

<span data-ttu-id="db7bd-201">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="db7bd-202">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="db7bd-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="db7bd-203">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-203">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-204">**Valeur par défaut**: nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="db7bd-205">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="db7bd-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="db7bd-206">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="db7bd-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-207">ContentRoot</span></span>

<span data-ttu-id="db7bd-208">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="db7bd-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="db7bd-209">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="db7bd-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="db7bd-210">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="db7bd-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="db7bd-211">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-211">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-212">**Par défaut**: le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="db7bd-213">**Variable d’environnement**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="db7bd-214">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="db7bd-215">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-215">For more information, see:</span></span>

* [<span data-ttu-id="db7bd-216">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="db7bd-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="db7bd-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="db7bd-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="db7bd-218">EnvironmentName</span></span>

<span data-ttu-id="db7bd-219">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="db7bd-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="db7bd-220">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="db7bd-221">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="db7bd-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="db7bd-222">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="db7bd-222">**Key**: `environment`</span></span>  
<span data-ttu-id="db7bd-223">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-223">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-224">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="db7bd-224">**Default**: `Production`</span></span>  
<span data-ttu-id="db7bd-225">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="db7bd-226">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="db7bd-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="db7bd-227">ShutdownTimeout</span></span>

<span data-ttu-id="db7bd-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="db7bd-229">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="db7bd-229">The default value is five seconds.</span></span>  <span data-ttu-id="db7bd-230">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="db7bd-231">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="db7bd-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="db7bd-232">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="db7bd-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="db7bd-233">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="db7bd-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="db7bd-234">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="db7bd-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="db7bd-235">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="db7bd-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="db7bd-236">**Clé** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="db7bd-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="db7bd-237">**Tapez**: `int`</span><span class="sxs-lookup"><span data-stu-id="db7bd-237">**Type**: `int`</span></span>  
<span data-ttu-id="db7bd-238">**Valeur par défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="db7bd-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="db7bd-239">**Variable d’environnement**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="db7bd-240">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="db7bd-241">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="db7bd-242">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="db7bd-242">Settings for web apps</span></span>

<span data-ttu-id="db7bd-243">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="db7bd-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="db7bd-244">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="db7bd-245">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="db7bd-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="db7bd-246">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="db7bd-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="db7bd-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="db7bd-247">CaptureStartupErrors</span></span>

<span data-ttu-id="db7bd-248">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="db7bd-249">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="db7bd-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="db7bd-250">**Clé** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="db7bd-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="db7bd-251">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-252">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="db7bd-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="db7bd-253">**Variable d’environnement**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="db7bd-254">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="db7bd-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="db7bd-255">DetailedErrors</span></span>

<span data-ttu-id="db7bd-256">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="db7bd-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="db7bd-257">**Clé** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="db7bd-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="db7bd-258">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-259">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="db7bd-259">**Default**: `false`</span></span>  
<span data-ttu-id="db7bd-260">**Variable d’environnement**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="db7bd-261">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="db7bd-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="db7bd-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="db7bd-263">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db7bd-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="db7bd-264">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="db7bd-265">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db7bd-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="db7bd-266">**Clé** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="db7bd-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="db7bd-267">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-267">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-268">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="db7bd-268">**Default**: Empty string</span></span>  
<span data-ttu-id="db7bd-269">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="db7bd-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="db7bd-270">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="db7bd-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="db7bd-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="db7bd-272">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db7bd-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="db7bd-273">**Clé** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="db7bd-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="db7bd-274">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-274">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-275">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="db7bd-275">**Default**: Empty string</span></span>  
<span data-ttu-id="db7bd-276">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="db7bd-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="db7bd-277">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="db7bd-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="db7bd-278">HTTPS_Port</span></span>

<span data-ttu-id="db7bd-279">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="db7bd-279">The HTTPS redirect port.</span></span> <span data-ttu-id="db7bd-280">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="db7bd-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="db7bd-281">**Clé** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="db7bd-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="db7bd-282">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-282">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-283">Valeur **par défaut**: aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="db7bd-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="db7bd-284">**Variable d’environnement**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="db7bd-285">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="db7bd-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="db7bd-286">PreferHostingUrls</span></span>

<span data-ttu-id="db7bd-287">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="db7bd-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="db7bd-288">**Clé** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="db7bd-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="db7bd-289">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-290">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="db7bd-290">**Default**: `true`</span></span>  
<span data-ttu-id="db7bd-291">**Variable d’environnement**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="db7bd-292">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="db7bd-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="db7bd-293">PreventHostingStartup</span></span>

<span data-ttu-id="db7bd-294">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="db7bd-295">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="db7bd-296">**Clé** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="db7bd-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="db7bd-297">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-298">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="db7bd-298">**Default**: `false`</span></span>  
<span data-ttu-id="db7bd-299">**Variable d’environnement**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="db7bd-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="db7bd-300">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="db7bd-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="db7bd-301">StartupAssembly</span></span>

<span data-ttu-id="db7bd-302">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="db7bd-303">**Clé** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="db7bd-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="db7bd-304">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-304">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-305">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="db7bd-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="db7bd-306">**Variable d’environnement**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="db7bd-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="db7bd-307">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="db7bd-308">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="db7bd-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="db7bd-309">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="db7bd-310">URLs</span><span class="sxs-lookup"><span data-stu-id="db7bd-310">URLs</span></span>

<span data-ttu-id="db7bd-311">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="db7bd-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="db7bd-312">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="db7bd-313">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="db7bd-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="db7bd-314">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="db7bd-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="db7bd-315">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="db7bd-316">**Clé** : `urls`</span><span class="sxs-lookup"><span data-stu-id="db7bd-316">**Key**: `urls`</span></span>  
<span data-ttu-id="db7bd-317">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-317">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-318">**Par défaut**: `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="db7bd-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="db7bd-319">**Variable d’environnement**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="db7bd-320">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="db7bd-321">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="db7bd-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="db7bd-322">Pour plus d'informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="db7bd-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-323">WebRoot</span></span>

<span data-ttu-id="db7bd-324">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="db7bd-325">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="db7bd-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="db7bd-326">**Clé** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="db7bd-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="db7bd-327">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-327">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-328">**Valeur par défaut**: la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="db7bd-329">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="db7bd-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="db7bd-330">**Variable d’environnement**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="db7bd-331">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="db7bd-332">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-332">For more information, see:</span></span>

* [<span data-ttu-id="db7bd-333">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="db7bd-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="db7bd-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="db7bd-335">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-335">Manage the host lifetime</span></span>

<span data-ttu-id="db7bd-336">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="db7bd-337">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="db7bd-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="db7bd-338">Exécuter</span><span class="sxs-lookup"><span data-stu-id="db7bd-338">Run</span></span>

<span data-ttu-id="db7bd-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="db7bd-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-340">RunAsync</span></span>

<span data-ttu-id="db7bd-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="db7bd-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="db7bd-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-342">RunConsoleAsync</span></span>

<span data-ttu-id="db7bd-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="db7bd-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="db7bd-344">Démarrer</span><span class="sxs-lookup"><span data-stu-id="db7bd-344">Start</span></span>

<span data-ttu-id="db7bd-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="db7bd-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="db7bd-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-346">StartAsync</span></span>

<span data-ttu-id="db7bd-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="db7bd-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="db7bd-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="db7bd-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="db7bd-349">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="db7bd-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="db7bd-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-350">StopAsync</span></span>

<span data-ttu-id="db7bd-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="db7bd-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="db7bd-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="db7bd-352">WaitForShutdown</span></span>

<span data-ttu-id="db7bd-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="db7bd-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="db7bd-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="db7bd-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="db7bd-356">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="db7bd-356">External control</span></span>

<span data-ttu-id="db7bd-357">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="db7bd-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="db7bd-358">Les applications ASP.NET Core configurent et lancent un hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="db7bd-359">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="db7bd-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="db7bd-360">Cet article traite de l’hôte générique ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), qui est utilisé pour les applications qui ne traitent pas les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="db7bd-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="db7bd-361">L’objectif de l’hôte générique est de séparer le pipeline HTTP de l’API Hôte web pour permettre un plus large éventail de scénarios d’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="db7bd-362">La messagerie, les tâches en arrière-plan et autres charges de travail non HTTP basées sur l’hôte générique bénéficient de fonctionnalités transversales, comme la configuration, l’injection de dépendances (DI) et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="db7bd-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="db7bd-363">L’hôte générique est nouveau dans ASP.NET Core 2.1 et n’est pas adapté aux scénarios d’hébergement web.</span><span class="sxs-lookup"><span data-stu-id="db7bd-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="db7bd-364">Pour les scénarios d’hébergement de web, utilisez l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="db7bd-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="db7bd-365">L’hôte générique est appelé à remplacer l’hôte web dans une version ultérieure et à servir d’API hôte principale dans les scénarios HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="db7bd-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="db7bd-366">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db7bd-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="db7bd-367">Quand vous exécutez l’exemple d’application dans [Visual Studio Code](https://code.visualstudio.com/), utilisez un *terminal externe ou intégré*.</span><span class="sxs-lookup"><span data-stu-id="db7bd-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="db7bd-368">N’exécutez pas l’exemple dans une `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="db7bd-369">Pour définir la console dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="db7bd-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="db7bd-370">Ouvrez le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="db7bd-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="db7bd-371">Dans la configuration **.NET Core Launch (console)**, recherchez l’entrée **console**.</span><span class="sxs-lookup"><span data-stu-id="db7bd-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="db7bd-372">Définissez la valeur avec `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="db7bd-373">Introduction</span><span class="sxs-lookup"><span data-stu-id="db7bd-373">Introduction</span></span>

<span data-ttu-id="db7bd-374">La bibliothèque de l’hôte générique est disponible dans l’espace de noms <xref:Microsoft.Extensions.Hosting> et est fournie par le package [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="db7bd-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="db7bd-375">Le package `Microsoft.Extensions.Hosting` est inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou ultérieur).</span><span class="sxs-lookup"><span data-stu-id="db7bd-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="db7bd-376"><xref:Microsoft.Extensions.Hosting.IHostedService> est le point d’entrée pour exécuter le code.</span><span class="sxs-lookup"><span data-stu-id="db7bd-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="db7bd-377">Chaque implémentation <xref:Microsoft.Extensions.Hosting.IHostedService> est exécutée dans l’ordre d’[inscription des services dans ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="db7bd-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="db7bd-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> est appelé sur chaque <xref:Microsoft.Extensions.Hosting.IHostedService> au démarrage de l’hôte, tandis que <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> est appelé dans l’ordre d’inscription inverse quand l’hôte s’arrête normalement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="db7bd-379">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-379">Set up a host</span></span>

<span data-ttu-id="db7bd-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> est le principal composant que les applications et les bibliothèques utilisent pour initialiser, générer et exécuter l’hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="db7bd-381">Options</span><span class="sxs-lookup"><span data-stu-id="db7bd-381">Options</span></span>

<span data-ttu-id="db7bd-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure les options pour <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="db7bd-383">Délai d’expiration</span><span class="sxs-lookup"><span data-stu-id="db7bd-383">Shutdown timeout</span></span>

<span data-ttu-id="db7bd-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="db7bd-385">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="db7bd-385">The default value is five seconds.</span></span>

<span data-ttu-id="db7bd-386">La configuration d’option suivante dans `Program.Main` augmente le délai d’attente d’arrêt de cinq secondes par défaut à 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="db7bd-387">Services par défaut</span><span class="sxs-lookup"><span data-stu-id="db7bd-387">Default services</span></span>

<span data-ttu-id="db7bd-388">Les services suivants sont inscrits au moment de l’initialisation de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="db7bd-389">[Environnement](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="db7bd-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="db7bd-390">[Configuration](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="db7bd-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="db7bd-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="db7bd-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="db7bd-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="db7bd-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="db7bd-393">[Options](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="db7bd-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="db7bd-394">[Journalisation](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="db7bd-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="db7bd-395">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-395">Host configuration</span></span>

<span data-ttu-id="db7bd-396">La création de la configuration d’hôte fait appel aux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-396">Host configuration is created by:</span></span>

* <span data-ttu-id="db7bd-397">Appel de méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder> pour définir la [racine du contenu](#content-root) et [l’environnement](#environment).</span><span class="sxs-lookup"><span data-stu-id="db7bd-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="db7bd-398">Lecture de la configuration à partir des fournisseurs de configuration dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="db7bd-399">Méthodes d’extension</span><span class="sxs-lookup"><span data-stu-id="db7bd-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="db7bd-400">Clé d’application (nom)</span><span class="sxs-lookup"><span data-stu-id="db7bd-400">Application key (name)</span></span>

<span data-ttu-id="db7bd-401">La propriété [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) est définie à partir de la configuration de l’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="db7bd-402">Pour définir la valeur explicitement, utilisez [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) :</span><span class="sxs-lookup"><span data-stu-id="db7bd-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="db7bd-403">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="db7bd-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="db7bd-404">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-404">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-405">**Par défaut** : nom de l’assembly contenant le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="db7bd-406">**Définir à l’aide**de : `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="db7bd-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="db7bd-407">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="db7bd-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="db7bd-408">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="db7bd-408">Content root</span></span>

<span data-ttu-id="db7bd-409">Ce paramètre détermine où l’hôte commence la recherche des fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="db7bd-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="db7bd-410">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="db7bd-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="db7bd-411">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-411">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-412">**Valeur par défaut** : dossier où réside l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="db7bd-413">**Définir à l’aide**de : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="db7bd-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="db7bd-414">**Variable d’environnement**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="db7bd-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="db7bd-415">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="db7bd-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="db7bd-416">Pour plus d’informations, consultez [principes de base : racine du contenu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="db7bd-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="db7bd-417">Environnement</span><span class="sxs-lookup"><span data-stu-id="db7bd-417">Environment</span></span>

<span data-ttu-id="db7bd-418">Définit l' [environnement](xref:fundamentals/environments)de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="db7bd-419">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="db7bd-419">**Key**: `environment`</span></span>  
<span data-ttu-id="db7bd-420">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-420">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-421">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="db7bd-421">**Default**: `Production`</span></span>  
<span data-ttu-id="db7bd-422">**Définir à l’aide**de : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="db7bd-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="db7bd-423">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` est [facultatif et défini par l’utilisateur](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="db7bd-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="db7bd-424">L’environnement peut être défini à n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="db7bd-424">The environment can be set to any value.</span></span> <span data-ttu-id="db7bd-425">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="db7bd-426">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="db7bd-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="db7bd-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="db7bd-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="db7bd-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="db7bd-429">La configuration d’hôte permet d’initialiser le <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> en vue de son utilisation dans le processus de génération de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="db7bd-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="db7bd-431">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="db7bd-432">Aucun fournisseur n’est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-432">No providers are included by default.</span></span> <span data-ttu-id="db7bd-433">Vous devez spécifier explicitement les fournisseurs de configuration dont l’application a besoin dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, notamment :</span><span class="sxs-lookup"><span data-stu-id="db7bd-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="db7bd-434">Configuration du fichier (par exemple, à partir d’un fichier *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="db7bd-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="db7bd-435">Configuration de la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-435">Environment variable configuration.</span></span>
* <span data-ttu-id="db7bd-436">Configuration de l’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="db7bd-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="db7bd-437">Tout autre fournisseur de configuration obligatoire.</span><span class="sxs-lookup"><span data-stu-id="db7bd-437">Any other required configuration providers.</span></span>

<span data-ttu-id="db7bd-438">Pour activer la configuration de fichier de l’hôte, spécifiez le chemin de base de l’application avec `SetBasePath`, suivi d’un appel à l’un des [fournisseurs de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db7bd-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="db7bd-439">L’exemple d’application utilise un fichier JSON, *hostsettings.json*, et appelle <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pour utiliser les paramètres de configuration d’hôte du fichier.</span><span class="sxs-lookup"><span data-stu-id="db7bd-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="db7bd-440">Pour ajouter la [configuration de variable d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider) de l’hôte, appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur le générateur d’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="db7bd-441">`AddEnvironmentVariables` accepte un préfixe facultatif défini par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="db7bd-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="db7bd-442">L’exemple d’application utilise un préfixe `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="db7bd-443">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="db7bd-444">Lorsque l’hôte de l’exemple d’application est configuré, la valeur de variable d’environnement de `PREFIX_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="db7bd-445">Pendant le développement, lorsque vous utilisez [Visual Studio](https://visualstudio.microsoft.com) ou que vous lancez une application avec `dotnet run`, vous pouvez définir les variables d’environnement dans le fichier *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="db7bd-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="db7bd-446">Dans [Visual Studio Code](https://code.visualstudio.com/), elles peuvent être définies dans le fichier *.vscode/launch.json* au cours du développement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="db7bd-447">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="db7bd-448">Pour ajouter la [configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider), appelez <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="db7bd-449">La configuration de ligne de commande est ajoutée en dernier pour permettre aux arguments de ligne de commande de substituer la configuration fournie par les fournisseurs de configuration antérieurs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="db7bd-450">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="db7bd-450">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="db7bd-451">Une configuration supplémentaire peut être fournie à l’aide des clés [applicationName](#application-key-name) et [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="db7bd-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="db7bd-452">Exemple de configuration `HostBuilder` avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="db7bd-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="db7bd-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="db7bd-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="db7bd-454">Pour créer la configuration d’application, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur l’implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="db7bd-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> utilise un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> pour créer un <xref:Microsoft.Extensions.Configuration.IConfiguration> pour l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="db7bd-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="db7bd-457">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="db7bd-458">La configuration créée par <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et dans <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="db7bd-459">La configuration d’application reçoit automatiquement la configuration d’hôte fournie par [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="db7bd-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="db7bd-460">Exemple de configuration d’application avec <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="db7bd-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="db7bd-461">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="db7bd-461">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="db7bd-462">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="db7bd-462">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="db7bd-463">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="db7bd-463">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="db7bd-464">Pour déplacer des fichiers de paramètres vers le répertoire de sortie, spécifiez-les en tant qu’[éléments de projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="db7bd-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="db7bd-465">L’exemple d’application déplace ses fichiers de paramètres d’application JSON et *hostsettings.json* avec l’élément `<Content>` suivant :</span><span class="sxs-lookup"><span data-stu-id="db7bd-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="db7bd-466">Les méthodes d’extension de configuration, telles que <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> et <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, nécessitent des packages NuGet supplémentaires, tels que [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) et [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="db7bd-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="db7bd-467">Si l’application n’utilise pas le [métapaquet Microsoft.AspNetCore.App ](xref:fundamentals/metapackage-app), ces packages doivent être ajoutés au projet en plus du noyau [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="db7bd-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="db7bd-468">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="db7bd-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="db7bd-469">ConfigureServices</span></span>

<span data-ttu-id="db7bd-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> ajoute des services au conteneur [d’injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="db7bd-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="db7bd-472">Un service hébergé est une classe avec la logique de tâches en arrière-plan qui implémente l’interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="db7bd-473">Pour plus d'informations, consultez <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="db7bd-474">L’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise la méthode d’extension `AddHostedService` afin d’ajouter un service pour les événements de durée de vie, `LifetimeEventsHostedService`, et une tâche en arrière-plan chronométrée, `TimedHostedService`, à l’application :</span><span class="sxs-lookup"><span data-stu-id="db7bd-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="db7bd-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="db7bd-475">ConfigureLogging</span></span>

<span data-ttu-id="db7bd-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> ajoute un délégué pour configurer le <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fourni.</span><span class="sxs-lookup"><span data-stu-id="db7bd-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="db7bd-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="db7bd-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-478">UseConsoleLifetime</span></span>

<span data-ttu-id="db7bd-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="db7bd-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="db7bd-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> déverrouille les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="db7bd-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="db7bd-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est préinscrit comme implémentation de durée de vie par défaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="db7bd-482">La dernière durée de vie inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="db7bd-483">Configuration du conteneur</span><span class="sxs-lookup"><span data-stu-id="db7bd-483">Container configuration</span></span>

<span data-ttu-id="db7bd-484">Pour prendre en charge le branchement dans d’autres conteneurs, l’hôte peut accepter un <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="db7bd-485">L’ajout d’une fabrique ne fait pas partie de l’inscription de conteneur DI mais est plutôt une tâche un intrinsèque à l’hôte utilisée pour créer le conteneur DI concret.</span><span class="sxs-lookup"><span data-stu-id="db7bd-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="db7bd-486">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) remplace la fabrique par défaut utilisée pour créer le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="db7bd-487">La configuration de conteneur personnalisée est gérée par la méthode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="db7bd-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre une expérience fortement typée pour configurer le conteneur sur l’API hôte sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="db7bd-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="db7bd-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="db7bd-490">Créer un conteneur de service pour l’application :</span><span class="sxs-lookup"><span data-stu-id="db7bd-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="db7bd-491">Fournir une fabrique de conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="db7bd-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="db7bd-492">Utiliser la fabrique et configurer le conteneur de service personnalisé pour l’application :</span><span class="sxs-lookup"><span data-stu-id="db7bd-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="db7bd-493">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="db7bd-493">Extensibility</span></span>

<span data-ttu-id="db7bd-494">L’extensibilité de l’hôte est effectuée avec les méthodes d’extension sur <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="db7bd-495">L’exemple suivant montre comment une méthode d’extension étend une implémentation <xref:Microsoft.Extensions.Hosting.IHostBuilder> avec l’exemple [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) démontré dans <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="db7bd-496">Une application établit la méthode d'extension `UseHostedService` pour inscrire le service hébergé passé dans `T` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="db7bd-497">Gérer l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-497">Manage the host</span></span>

<span data-ttu-id="db7bd-498">L’implémentation <xref:Microsoft.Extensions.Hosting.IHost> est chargée de démarrer et d’arrêter les implémentations <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="db7bd-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="db7bd-499">Exécuter</span><span class="sxs-lookup"><span data-stu-id="db7bd-499">Run</span></span>

<span data-ttu-id="db7bd-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="db7bd-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-501">RunAsync</span></span>

<span data-ttu-id="db7bd-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand le jeton d’annulation ou l’arrêt est déclenché :</span><span class="sxs-lookup"><span data-stu-id="db7bd-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="db7bd-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-503">RunConsoleAsync</span></span>

<span data-ttu-id="db7bd-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="db7bd-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="db7bd-505">Start et StopAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-505">Start and StopAsync</span></span>

<span data-ttu-id="db7bd-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="db7bd-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="db7bd-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="db7bd-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="db7bd-508">StartAsync et StopAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="db7bd-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="db7bd-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arrête l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="db7bd-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="db7bd-511">WaitForShutdown</span></span>

<span data-ttu-id="db7bd-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>est déclenché via le <xref:Microsoft.Extensions.Hosting.IHostLifetime> , par exemple `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (écoute la <kbd>touche Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="db7bd-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="db7bd-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="db7bd-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="db7bd-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="db7bd-516">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="db7bd-516">External control</span></span>

<span data-ttu-id="db7bd-517">Le contrôle externe de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="db7bd-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="db7bd-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="db7bd-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="db7bd-519">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="db7bd-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="db7bd-520">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="db7bd-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="db7bd-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fournit des informations sur l’environnement d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="db7bd-522">Utilisez [l’injection de constructeur](xref:fundamentals/dependency-injection) pour obtenir l’interface <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> afin d’utiliser ses propriétés et méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="db7bd-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="db7bd-523">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="db7bd-524">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="db7bd-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> s’utilise pour les activités de post-démarrage et d’arrêt, notamment pour les demandes d’arrêt normal.</span><span class="sxs-lookup"><span data-stu-id="db7bd-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="db7bd-526">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes <xref:System.Action> qui définissent les événements de démarrage et d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="db7bd-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="db7bd-527">Jeton d’annulation</span><span class="sxs-lookup"><span data-stu-id="db7bd-527">Cancellation Token</span></span> | <span data-ttu-id="db7bd-528">Événement déclencheur</span><span class="sxs-lookup"><span data-stu-id="db7bd-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="db7bd-529">L’hôte a complètement démarré.</span><span class="sxs-lookup"><span data-stu-id="db7bd-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="db7bd-530">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="db7bd-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="db7bd-531">Toutes les requêtes doivent être traitées.</span><span class="sxs-lookup"><span data-stu-id="db7bd-531">All requests should be processed.</span></span> <span data-ttu-id="db7bd-532">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="db7bd-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="db7bd-533">L’hôte effectue un arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="db7bd-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="db7bd-534">Des requêtes peuvent encore être en cours de traitement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-534">Requests may still be processing.</span></span> <span data-ttu-id="db7bd-535">L’arrêt est bloqué tant que cet événement n’est pas terminé.</span><span class="sxs-lookup"><span data-stu-id="db7bd-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="db7bd-536">Le constructeur injecte le service <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> dans une classe.</span><span class="sxs-lookup"><span data-stu-id="db7bd-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="db7bd-537">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) utilise l’injection de constructeur dans une classe `LifetimeEventsHostedService` (une implémentation <xref:Microsoft.Extensions.Hosting.IHostedService>) pour inscrire les événements.</span><span class="sxs-lookup"><span data-stu-id="db7bd-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="db7bd-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="db7bd-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="db7bd-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> demande l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="db7bd-540">La classe suivante utilise <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pour arrêter normalement une application lors de l’appel de la méthode `Shutdown` de la classe :</span><span class="sxs-lookup"><span data-stu-id="db7bd-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="db7bd-541">Les modèles ASP.NET Core créent un hôte générique .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="db7bd-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="db7bd-542">Définition de l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-542">Host definition</span></span>

<span data-ttu-id="db7bd-543">Un *hôte* est un objet qui encapsule les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="db7bd-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="db7bd-544">Injection de dépendances (DI)</span><span class="sxs-lookup"><span data-stu-id="db7bd-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="db7bd-545">Journalisation</span><span class="sxs-lookup"><span data-stu-id="db7bd-545">Logging</span></span>
* <span data-ttu-id="db7bd-546">Configuration</span><span class="sxs-lookup"><span data-stu-id="db7bd-546">Configuration</span></span>
* <span data-ttu-id="db7bd-547">Implémentations de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="db7bd-547">`IHostedService` implementations</span></span>

<span data-ttu-id="db7bd-548">Lorsqu’un hôte démarre, il appelle <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> sur chaque implémentation de <xref:Microsoft.Extensions.Hosting.IHostedService> inscrite dans la collection de services hébergés du conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="db7bd-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="db7bd-549">Dans une application web, l’une des implémentations de `IHostedService` est un service web qui démarre une [implémentation de serveur HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="db7bd-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="db7bd-550">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="db7bd-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="db7bd-551">Configurer un hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-551">Set up a host</span></span>

<span data-ttu-id="db7bd-552">L’hôte est généralement configuré, généré et exécuté par du code dans la classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="db7bd-553">La méthode `Main` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-553">The `Main` method:</span></span>

* <span data-ttu-id="db7bd-554">Appelle une méthode `CreateHostBuilder` pour créer et configurer un objet builder.</span><span class="sxs-lookup"><span data-stu-id="db7bd-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="db7bd-555">Appelle les méthodes `Build` et `Run` sur l’objet builder.</span><span class="sxs-lookup"><span data-stu-id="db7bd-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="db7bd-556">Les modèles Web ASP.NET Core génèrent le code suivant pour créer un hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="db7bd-557">Le code suivant crée une charge de travail non-HTTP avec une `IHostedService` implémentation ajoutée au conteneur di.</span><span class="sxs-lookup"><span data-stu-id="db7bd-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="db7bd-558">Pour une charge de travail HTTP, la méthode `Main` est la même, mais `CreateHostBuilder` appelle `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="db7bd-559">Si l’application utilise Entity Framework Core, ne changez pas le nom ou la signature de la méthode `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="db7bd-560">Les [outils Entity Framework Core tools](/ef/core/miscellaneous/cli/) s’attendent à trouver une méthode `CreateHostBuilder` qui configure l’hôte sans exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="db7bd-561">Pour plus d’informations, consultez [Création de DbContext au moment du design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="db7bd-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="db7bd-562">Paramètres du générateur par défaut</span><span class="sxs-lookup"><span data-stu-id="db7bd-562">Default builder settings</span></span>

<span data-ttu-id="db7bd-563">La méthode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="db7bd-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="db7bd-564">Définit la [racine du contenu](xref:fundamentals/index#content-root) sur le chemin d’accès retourné par <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="db7bd-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="db7bd-565">Charge la configuration de l’hôte à partir de :</span><span class="sxs-lookup"><span data-stu-id="db7bd-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="db7bd-566">Les variables d’environnement précédées de `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="db7bd-567">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db7bd-567">Command-line arguments.</span></span>
* <span data-ttu-id="db7bd-568">Charge la configuration de l’application à partir de :</span><span class="sxs-lookup"><span data-stu-id="db7bd-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="db7bd-569">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="db7bd-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="db7bd-570">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="db7bd-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="db7bd-571">[Secret Manager](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="db7bd-572">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-572">Environment variables.</span></span>
  * <span data-ttu-id="db7bd-573">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db7bd-573">Command-line arguments.</span></span>
* <span data-ttu-id="db7bd-574">Ajoute les fournisseurs de [journalisation](xref:fundamentals/logging/index) suivants :</span><span class="sxs-lookup"><span data-stu-id="db7bd-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="db7bd-575">Console</span><span class="sxs-lookup"><span data-stu-id="db7bd-575">Console</span></span>
  * <span data-ttu-id="db7bd-576">Débogage</span><span class="sxs-lookup"><span data-stu-id="db7bd-576">Debug</span></span>
  * <span data-ttu-id="db7bd-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="db7bd-577">EventSource</span></span>
  * <span data-ttu-id="db7bd-578">EventLog (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="db7bd-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="db7bd-579">Active la [validation de l’étendue](xref:fundamentals/dependency-injection#scope-validation) et la [validation de dépendances](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) dans un environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="db7bd-580">La méthode `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="db7bd-581">Charge la configuration de l’hôte à partir de variables d’environnement précédées de `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="db7bd-582">Définit le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et le configure en utilisant les fournisseurs de configuration d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="db7bd-583">Pour connaître les options par défaut du serveur Kestrel, consultez <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="db7bd-584">Ajoute l’[intergiciel (middleware) Filtrage d’hôtes](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="db7bd-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="db7bd-585">Ajoute l' [intergiciel d’en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) si `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est égal à `true` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="db7bd-586">Permet l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="db7bd-586">Enables IIS integration.</span></span> <span data-ttu-id="db7bd-587">Pour connaître les options par défaut d’IIS, consultez <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="db7bd-588">Les sections [Paramètres pour tous les types d’applications](#settings-for-all-app-types) et [Paramètres pour les applications web](#settings-for-web-apps) figurant plus loin dans cet article montrent comment remplacer les paramètres du générateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="db7bd-589">Services fournis par le framework</span><span class="sxs-lookup"><span data-stu-id="db7bd-589">Framework-provided services</span></span>

<span data-ttu-id="db7bd-590">Les services suivants sont inscrits automatiquement :</span><span class="sxs-lookup"><span data-stu-id="db7bd-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="db7bd-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="db7bd-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="db7bd-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="db7bd-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="db7bd-594">Pour plus d’informations sur les services fournis par le Framework, consultez <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="db7bd-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="db7bd-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="db7bd-596">Injectez le service <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anciennement `IApplicationLifetime`) dans n’importe quelle classe pour gérer les tâches post-démarrage et d’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="db7bd-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="db7bd-597">Trois propriétés de l’interface sont des jetons d’annulation utilisés pour inscrire les méthodes du gestionnaire d’événements de démarrage et d’arrêt d’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="db7bd-598">L’interface inclut également une méthode `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="db7bd-599">L’exemple suivant est une `IHostedService` implémentation qui inscrit des `IHostApplicationLifetime` événements :</span><span class="sxs-lookup"><span data-stu-id="db7bd-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="db7bd-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="db7bd-600">IHostLifetime</span></span>

<span data-ttu-id="db7bd-601">L’implémentation de <xref:Microsoft.Extensions.Hosting.IHostLifetime> contrôle quand l’hôte démarre et quand il s’arrête.</span><span class="sxs-lookup"><span data-stu-id="db7bd-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="db7bd-602">La dernière implémentation inscrite est utilisée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-602">The last implementation registered is used.</span></span>

<span data-ttu-id="db7bd-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` est l’implémentation de `IHostLifetime` par défaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="db7bd-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="db7bd-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="db7bd-605">Écoute la <kbd>combinaison de touches Ctrl</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM et appelle <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pour démarrer le processus d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="db7bd-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="db7bd-606">Débloque les extensions telles que [RunAsync](#runasync) et [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="db7bd-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="db7bd-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="db7bd-607">IHostEnvironment</span></span>

<span data-ttu-id="db7bd-608">Injectez le <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service dans une classe pour obtenir des informations sur les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="db7bd-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="db7bd-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="db7bd-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="db7bd-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="db7bd-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="db7bd-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="db7bd-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="db7bd-612">Les applications Web implémentent l' `IWebHostEnvironment` interface, qui hérite `IHostEnvironment` et ajoute [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="db7bd-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="db7bd-613">Configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-613">Host configuration</span></span>

<span data-ttu-id="db7bd-614">La configuration de l’hôte est utilisée pour les propriétés de l’implémentation de <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="db7bd-615">La configuration de l’hôte est disponible à partir de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="db7bd-616">Après `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` est remplacé par la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="db7bd-617">Pour ajouter la configuration d’hôte, appelez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="db7bd-618">`ConfigureHostConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="db7bd-619">L’hôte utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="db7bd-620">Le fournisseur de variables d’environnement avec des arguments de préfixe `DOTNET_` et de ligne de commande est inclus par `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="db7bd-621">Pour les applications web, le fournisseur de variables d’environnement avec le préfixe `ASPNETCORE_` est ajouté.</span><span class="sxs-lookup"><span data-stu-id="db7bd-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="db7bd-622">Ce préfixe est supprimé à la lecture des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="db7bd-623">Par exemple, la valeur de variable d’environnement de `ASPNETCORE_ENVIRONMENT` devient la valeur de configuration d’hôte de la clé `environment`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="db7bd-624">L’exemple suivant crée la configuration d’hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="db7bd-625">la configuration d’une application ;</span><span class="sxs-lookup"><span data-stu-id="db7bd-625">App configuration</span></span>

<span data-ttu-id="db7bd-626">La configuration d’application est créée en appelant <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sur `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="db7bd-627">`ConfigureAppConfiguration` peut être appelé plusieurs fois avec des résultats additifs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="db7bd-628">L’application utilise l’option qui définit une valeur en dernier sur une clé donnée.</span><span class="sxs-lookup"><span data-stu-id="db7bd-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="db7bd-629">La configuration créée par `ConfigureAppConfiguration` est disponible dans [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pour les opérations suivantes et en tant que service à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="db7bd-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="db7bd-630">La configuration d’hôte est également ajoutée à la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="db7bd-631">Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="db7bd-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="db7bd-632">Paramètres pour tous les types d’applications</span><span class="sxs-lookup"><span data-stu-id="db7bd-632">Settings for all app types</span></span>

<span data-ttu-id="db7bd-633">Cette section liste les paramètres d’hôte qui s’appliquent aux charges de travail HTTP et non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="db7bd-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="db7bd-634">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="db7bd-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="db7bd-635">ApplicationName</span></span>

<span data-ttu-id="db7bd-636">La propriété [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) est définie à partir de la configuration d’hôte pendant la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="db7bd-637">**Clé** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="db7bd-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="db7bd-638">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-638">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-639">**Valeur par défaut**: nom de l’assembly qui contient le point d’entrée de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="db7bd-640">**Variable d’environnement**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="db7bd-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="db7bd-641">Pour définir cette valeur, utilisez la variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db7bd-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="db7bd-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-642">ContentRoot</span></span>

<span data-ttu-id="db7bd-643">La propriété [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) détermine où l’hôte commence à rechercher les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="db7bd-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="db7bd-644">Si le chemin est introuvable, l’hôte ne peut pas démarrer.</span><span class="sxs-lookup"><span data-stu-id="db7bd-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="db7bd-645">**Clé** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="db7bd-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="db7bd-646">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-646">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-647">**Par défaut**: le dossier dans lequel se trouve l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="db7bd-648">**Variable d’environnement**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="db7bd-649">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseContentRoot` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="db7bd-650">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-650">For more information, see:</span></span>

* [<span data-ttu-id="db7bd-651">Notions de base : racine du contenu</span><span class="sxs-lookup"><span data-stu-id="db7bd-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="db7bd-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="db7bd-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="db7bd-653">EnvironmentName</span></span>

<span data-ttu-id="db7bd-654">La propriété [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) peut être définie avec n’importe quelle valeur.</span><span class="sxs-lookup"><span data-stu-id="db7bd-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="db7bd-655">Les valeurs définies par le framework sont `Development`, `Staging` et `Production`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="db7bd-656">Les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="db7bd-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="db7bd-657">**Clé** : `environment`</span><span class="sxs-lookup"><span data-stu-id="db7bd-657">**Key**: `environment`</span></span>  
<span data-ttu-id="db7bd-658">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-658">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-659">**Par défaut**: `Production`</span><span class="sxs-lookup"><span data-stu-id="db7bd-659">**Default**: `Production`</span></span>  
<span data-ttu-id="db7bd-660">**Variable d’environnement**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="db7bd-661">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseEnvironment` sur `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="db7bd-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="db7bd-662">ShutdownTimeout</span></span>

<span data-ttu-id="db7bd-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) définit le délai d’expiration pour <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="db7bd-664">La valeur par défaut est de cinq secondes.</span><span class="sxs-lookup"><span data-stu-id="db7bd-664">The default value is five seconds.</span></span>  <span data-ttu-id="db7bd-665">Pendant la période du délai d’expiration, l’hôte :</span><span class="sxs-lookup"><span data-stu-id="db7bd-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="db7bd-666">Déclenche [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="db7bd-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="db7bd-667">Tente d’arrêter les services hébergés, en journalisant les erreurs pour les services qui échouent à s’arrêter.</span><span class="sxs-lookup"><span data-stu-id="db7bd-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="db7bd-668">Si la période du délai d’attente expire avant l’arrêt de tous les services hébergés, les services actifs restants sont arrêtés quand l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="db7bd-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="db7bd-669">Les services s’arrêtent même s’ils n’ont pas terminé les traitements.</span><span class="sxs-lookup"><span data-stu-id="db7bd-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="db7bd-670">Si des services nécessitent plus de temps pour s’arrêter, augmentez le délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="db7bd-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="db7bd-671">**Clé** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="db7bd-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="db7bd-672">**Tapez**: `int`</span><span class="sxs-lookup"><span data-stu-id="db7bd-672">**Type**: `int`</span></span>  
<span data-ttu-id="db7bd-673">**Valeur par défaut**: 5 secondes</span><span class="sxs-lookup"><span data-stu-id="db7bd-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="db7bd-674">**Variable d’environnement**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="db7bd-675">Pour définir cette valeur, utilisez la variable d’environnement ou configurez `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="db7bd-676">L’exemple suivant définit un délai d’expiration de 20 secondes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="db7bd-677">Désactiver le rechargement de la configuration d’application lors de la modification</span><span class="sxs-lookup"><span data-stu-id="db7bd-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="db7bd-678">Par [défaut](xref:fundamentals/configuration/index#default), *appsettings.jssur* et *appSettings. { Environment}. JSON* est rechargé lorsque le fichier change.</span><span class="sxs-lookup"><span data-stu-id="db7bd-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="db7bd-679">Pour désactiver ce comportement de rechargement dans ASP.NET Core 5,0 Preview 3 ou version ultérieure, affectez la valeur `hostBuilder:reloadConfigOnChange` à la clé `false` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="db7bd-680">**Clé** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="db7bd-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="db7bd-681">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-682">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="db7bd-682">**Default**: `true`</span></span>  
<span data-ttu-id="db7bd-683">**Argument de ligne de commande**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="db7bd-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="db7bd-684">**Variable d’environnement**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="db7bd-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="db7bd-685">Le séparateur deux `:` -points () ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="db7bd-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="db7bd-686">Pour en savoir plus, voir [Variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="db7bd-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="db7bd-687">Paramètres pour les applications web</span><span class="sxs-lookup"><span data-stu-id="db7bd-687">Settings for web apps</span></span>

<span data-ttu-id="db7bd-688">Certains paramètres d’hôte s’appliquent uniquement aux charges de travail HTTP.</span><span class="sxs-lookup"><span data-stu-id="db7bd-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="db7bd-689">Par défaut, les variables d’environnement utilisées pour configurer ces paramètres peuvent avoir un préfixe `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="db7bd-690">Des méthodes d’extension sur `IWebHostBuilder` sont disponibles pour ces paramètres.</span><span class="sxs-lookup"><span data-stu-id="db7bd-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="db7bd-691">Les exemples de code qui montrent comment appeler les méthodes d’extension supposent que `webBuilder` est une instance de `IWebHostBuilder`, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="db7bd-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="db7bd-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="db7bd-692">CaptureStartupErrors</span></span>

<span data-ttu-id="db7bd-693">Avec la valeur `false`, la survenue d’erreurs au démarrage entraîne la fermeture de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="db7bd-694">Avec la valeur `true`, l’hôte capture les exceptions levées au démarrage et tente de démarrer le serveur.</span><span class="sxs-lookup"><span data-stu-id="db7bd-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="db7bd-695">**Clé** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="db7bd-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="db7bd-696">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-697">**Valeur par défaut** : `false`, ou `true` si l’application s’exécute avec Kestrel derrière IIS.</span><span class="sxs-lookup"><span data-stu-id="db7bd-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="db7bd-698">**Variable d’environnement**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="db7bd-699">Pour définir cette valeur, utilisez la configuration ou appelez `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="db7bd-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="db7bd-700">DetailedErrors</span></span>

<span data-ttu-id="db7bd-701">En cas d’activation ou quand l’environnement est `Development`, l’application capture des erreurs détaillées.</span><span class="sxs-lookup"><span data-stu-id="db7bd-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="db7bd-702">**Clé** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="db7bd-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="db7bd-703">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-704">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="db7bd-704">**Default**: `false`</span></span>  
<span data-ttu-id="db7bd-705">**Variable d’environnement**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="db7bd-706">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="db7bd-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="db7bd-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="db7bd-708">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à charger au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db7bd-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="db7bd-709">La valeur de configuration par défaut est une chaîne vide, mais les assemblys d’hébergement au démarrage incluent toujours l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="db7bd-710">Quand des assemblys d’hébergement au démarrage sont fournis, ils sont ajoutés à l’assembly de l’application et sont chargés lorsque l’application génère ses services communs au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db7bd-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="db7bd-711">**Clé** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="db7bd-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="db7bd-712">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-712">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-713">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="db7bd-713">**Default**: Empty string</span></span>  
<span data-ttu-id="db7bd-714">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="db7bd-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="db7bd-715">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="db7bd-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="db7bd-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="db7bd-717">Chaîne délimitée par des points-virgules qui spécifie les assemblys d’hébergement à exclure au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db7bd-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="db7bd-718">**Clé** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="db7bd-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="db7bd-719">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-719">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-720">**Valeur par défaut**: chaîne vide</span><span class="sxs-lookup"><span data-stu-id="db7bd-720">**Default**: Empty string</span></span>  
<span data-ttu-id="db7bd-721">**Variable d’environnement**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="db7bd-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="db7bd-722">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="db7bd-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="db7bd-723">HTTPS_Port</span></span>

<span data-ttu-id="db7bd-724">Port de redirection HTTPS.</span><span class="sxs-lookup"><span data-stu-id="db7bd-724">The HTTPS redirect port.</span></span> <span data-ttu-id="db7bd-725">Utilisé dans [l’application de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="db7bd-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="db7bd-726">**Clé** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="db7bd-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="db7bd-727">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-727">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-728">Valeur **par défaut**: aucune valeur par défaut n’est définie.</span><span class="sxs-lookup"><span data-stu-id="db7bd-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="db7bd-729">**Variable d’environnement**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="db7bd-730">Pour définir cette valeur, utilisez la configuration ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="db7bd-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="db7bd-731">PreferHostingUrls</span></span>

<span data-ttu-id="db7bd-732">Indique si l’hôte doit écouter les URL configurées avec le `IWebHostBuilder` au lieu des URL configurées avec l' `IServer` implémentation.</span><span class="sxs-lookup"><span data-stu-id="db7bd-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="db7bd-733">**Clé** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="db7bd-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="db7bd-734">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-735">**Par défaut**: `true`</span><span class="sxs-lookup"><span data-stu-id="db7bd-735">**Default**: `true`</span></span>  
<span data-ttu-id="db7bd-736">**Variable d’environnement**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="db7bd-737">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="db7bd-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="db7bd-738">PreventHostingStartup</span></span>

<span data-ttu-id="db7bd-739">Empêche le chargement automatique des assemblys d’hébergement au démarrage, y compris ceux configurés par l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="db7bd-740">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="db7bd-741">**Clé** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="db7bd-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="db7bd-742">**Type**: `bool` ( `true` ou `1` )</span><span class="sxs-lookup"><span data-stu-id="db7bd-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="db7bd-743">**Par défaut**: `false`</span><span class="sxs-lookup"><span data-stu-id="db7bd-743">**Default**: `false`</span></span>  
<span data-ttu-id="db7bd-744">**Variable d’environnement**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="db7bd-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="db7bd-745">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="db7bd-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="db7bd-746">StartupAssembly</span></span>

<span data-ttu-id="db7bd-747">Assembly où rechercher la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="db7bd-748">**Clé** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="db7bd-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="db7bd-749">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-749">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-750">**Valeur par défaut** : l’assembly de l’application</span><span class="sxs-lookup"><span data-stu-id="db7bd-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="db7bd-751">**Variable d’environnement**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="db7bd-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="db7bd-752">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="db7bd-753">`UseStartup` peut prendre un nom d’assembly (`string`) ou un type (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="db7bd-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="db7bd-754">Si plusieurs méthodes `UseStartup` sont appelées, la dernière prévaut.</span><span class="sxs-lookup"><span data-stu-id="db7bd-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="db7bd-755">URLs</span><span class="sxs-lookup"><span data-stu-id="db7bd-755">URLs</span></span>

<span data-ttu-id="db7bd-756">Liste délimitée par des points-virgules d’adresses IP ou d’adresses d’hôte avec les ports et protocoles sur lesquels le serveur doit écouter les requêtes.</span><span class="sxs-lookup"><span data-stu-id="db7bd-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="db7bd-757">Par exemple : `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="db7bd-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="db7bd-758">Utilisez « \* » pour indiquer que le serveur doit écouter les requêtes sur toutes les adresses IP ou tous les noms d’hôte qui utilisent le port et le protocole spécifiés (par exemple, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="db7bd-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="db7bd-759">Le protocole (`http://` ou `https://`) doit être inclus avec chaque URL.</span><span class="sxs-lookup"><span data-stu-id="db7bd-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="db7bd-760">Les formats pris en charge varient selon les serveurs.</span><span class="sxs-lookup"><span data-stu-id="db7bd-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="db7bd-761">**Clé** : `urls`</span><span class="sxs-lookup"><span data-stu-id="db7bd-761">**Key**: `urls`</span></span>  
<span data-ttu-id="db7bd-762">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-762">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-763">**Par défaut**: `http://localhost:5000` et `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="db7bd-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="db7bd-764">**Variable d’environnement**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="db7bd-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="db7bd-765">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="db7bd-766">Kestrel a sa propre API de configuration de points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="db7bd-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="db7bd-767">Pour plus d'informations, consultez <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="db7bd-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-768">WebRoot</span></span>

<span data-ttu-id="db7bd-769">La propriété [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) détermine le chemin d’accès relatif aux ressources statiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="db7bd-770">Si ce chemin n’existe pas, un fournisseur de fichiers no-op est utilisé.</span><span class="sxs-lookup"><span data-stu-id="db7bd-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="db7bd-771">**Clé** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="db7bd-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="db7bd-772">**Tapez**: `string`</span><span class="sxs-lookup"><span data-stu-id="db7bd-772">**Type**: `string`</span></span>  
<span data-ttu-id="db7bd-773">**Valeur par défaut**: la valeur par défaut est `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="db7bd-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="db7bd-774">Le chemin d’accès à *{root content}/wwwroot* doit exister.</span><span class="sxs-lookup"><span data-stu-id="db7bd-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="db7bd-775">**Variable d’environnement**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="db7bd-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="db7bd-776">Pour définir cette valeur, utilisez la variable d’environnement ou appelez `UseWebRoot` sur `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="db7bd-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="db7bd-777">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="db7bd-777">For more information, see:</span></span>

* [<span data-ttu-id="db7bd-778">Notions de base : racine Web</span><span class="sxs-lookup"><span data-stu-id="db7bd-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="db7bd-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="db7bd-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="db7bd-780">Gérer la durée de vie de l’hôte</span><span class="sxs-lookup"><span data-stu-id="db7bd-780">Manage the host lifetime</span></span>

<span data-ttu-id="db7bd-781">Appelez des méthodes sur l’implémentation de <xref:Microsoft.Extensions.Hosting.IHost> pour démarrer et arrêter l’application.</span><span class="sxs-lookup"><span data-stu-id="db7bd-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="db7bd-782">Ces méthodes affectent toutes les implémentations de <xref:Microsoft.Extensions.Hosting.IHostedService> qui sont inscrites dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="db7bd-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="db7bd-783">Exécuter</span><span class="sxs-lookup"><span data-stu-id="db7bd-783">Run</span></span>

<span data-ttu-id="db7bd-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> exécute l’application et bloque le thread appelant jusqu’à l’arrêt de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db7bd-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="db7bd-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-785">RunAsync</span></span>

<span data-ttu-id="db7bd-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> exécute l’application et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="db7bd-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="db7bd-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-787">RunConsoleAsync</span></span>

<span data-ttu-id="db7bd-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>active la prise en charge de la console, crée et démarre l’hôte, puis attend que <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM s’arrête.</span><span class="sxs-lookup"><span data-stu-id="db7bd-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="db7bd-789">Démarrer</span><span class="sxs-lookup"><span data-stu-id="db7bd-789">Start</span></span>

<span data-ttu-id="db7bd-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> démarre l’hôte en mode synchrone.</span><span class="sxs-lookup"><span data-stu-id="db7bd-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="db7bd-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-791">StartAsync</span></span>

<span data-ttu-id="db7bd-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> démarre l’hôte et retourne un <xref:System.Threading.Tasks.Task> qui est effectué quand le jeton d’annulation ou l’arrêt est déclenché.</span><span class="sxs-lookup"><span data-stu-id="db7bd-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="db7bd-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> est appelé au début de `StartAsync`, lequel attend qu’il soit fini avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="db7bd-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="db7bd-794">Cela permet de retarder le démarrage jusqu'à ce que celui-ci soit signalé par un événement externe.</span><span class="sxs-lookup"><span data-stu-id="db7bd-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="db7bd-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-795">StopAsync</span></span>

<span data-ttu-id="db7bd-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tente d’arrêter l’hôte dans le délai d’attente fourni.</span><span class="sxs-lookup"><span data-stu-id="db7bd-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="db7bd-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="db7bd-797">WaitForShutdown</span></span>

<span data-ttu-id="db7bd-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>bloque le thread appelant jusqu’à ce que l’arrêt soit déclenché par IHostLifetime, par exemple via <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="db7bd-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="db7bd-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="db7bd-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="db7bd-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retourne une <xref:System.Threading.Tasks.Task> qui est effectuée quand l’arrêt est déclenché par le biais du jeton fourni et appelle <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="db7bd-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="db7bd-801">Contrôle externe</span><span class="sxs-lookup"><span data-stu-id="db7bd-801">External control</span></span>

<span data-ttu-id="db7bd-802">Le contrôle direct de la durée de vie de l’hôte peut être effectué à l’aide de méthodes pouvant être appelées de façon externe :</span><span class="sxs-lookup"><span data-stu-id="db7bd-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="db7bd-803">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="db7bd-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
