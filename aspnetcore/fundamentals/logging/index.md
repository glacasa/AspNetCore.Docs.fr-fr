---
title: Journalisation dans .NET Core et ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le framework de journalisation fourni par le package NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: b897d0d775da62a11f01a64f39b47b6c5abebc8b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791560"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="f9d82-103">Journalisation dans .NET Core et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9d82-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9d82-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f9d82-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f9d82-105">.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f9d82-106">Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f9d82-107">La plupart des exemples de code présentés dans cet article proviennent d’applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9d82-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="f9d82-108">Les parties spécifiques à l’enregistrement de ces extraits de code s’appliquent à toute application .NET Core qui utilise [l’hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="f9d82-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="f9d82-109">Pour un exemple de la façon d’utiliser l’hôte générique dans une application de console<xref:fundamentals/host/hosted-services>non-web, consultez le fichier *Program.cs* de l’application [d’échantillon De tâches d’arrière-plan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).</span><span class="sxs-lookup"><span data-stu-id="f9d82-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="f9d82-110">Le code de journalisation pour les applications sans hôte générique diffère dans la façon dont les [fournisseurs sont ajoutés](#add-providers) et les [enregistreurs d'événements créés](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="f9d82-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="f9d82-111">Des exemples de code non hôte sont présentés dans ces sections de l’article.</span><span class="sxs-lookup"><span data-stu-id="f9d82-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="f9d82-112">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9d82-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f9d82-113">Ajouter des fournisseurs</span><span class="sxs-lookup"><span data-stu-id="f9d82-113">Add providers</span></span>

<span data-ttu-id="f9d82-114">Un fournisseur de journalisation affiche ou stocke des journaux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f9d82-115">Par exemple, le fournisseur Console affiche les journaux dans la console, et le fournisseur Azure Application Insights les stocke dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f9d82-116">Il est possible d’envoyer les journaux à plusieurs endroits en ajoutant plusieurs fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="f9d82-117">Pour ajouter un fournisseur dans une application qui utilise un hôte générique, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="f9d82-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="f9d82-118">Dans une application de console non hôte, appelez la méthode d’extension `Add{provider name}` du fournisseur lors de la création d’un `LoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="f9d82-119">`LoggerFactory` et `AddConsole` requièrent une instruction `using` pour `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="f9d82-120">Les modèles de projet ASP.NET Core par défaut appellent <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="f9d82-121">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="f9d82-122">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f9d82-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="f9d82-124">[EventLog](#windows-eventlog-provider) (seulement lorsqu’il s’exécute sur Windows)</span><span class="sxs-lookup"><span data-stu-id="f9d82-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="f9d82-125">Vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix.</span><span class="sxs-lookup"><span data-stu-id="f9d82-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="f9d82-126">Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="f9d82-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="f9d82-127">Vous trouverez des informations sur les [fournisseurs de journalisation intégrés](#built-in-logging-providers) et les [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f9d82-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f9d82-128">Créer des journaux</span><span class="sxs-lookup"><span data-stu-id="f9d82-128">Create logs</span></span>

<span data-ttu-id="f9d82-129">Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f9d82-130">Dans une application web ou un service hébergé, obtenez un `ILogger` à partir de l’injection de dépendances (DI).</span><span class="sxs-lookup"><span data-stu-id="f9d82-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f9d82-131">Dans les applications de console non hôtes, utilisez le `LoggerFactory` pour créer un `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f9d82-132">L’exemple d’ASP.NET Core suivant crée un enregistreur d’événements de catégorie `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f9d82-133">La *catégorie* du journal est une chaîne associée à chaque journal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f9d82-134">L’instance `ILogger<T>` fournie par l’injection de dépendances crée des journaux ayant comme catégorie un nom complet de type `T`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f9d82-135">L’exemple d’application de console non hôte suivant crée un enregistreur d’événements de catégorie `LoggingConsoleApp.Program`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="f9d82-136">Dans les exemples d’ASP.NET Core et d’application de console suivants, l’enregistreur d’événements est utilisé pour créer des journaux de niveau `Information`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f9d82-137">Le *niveau* du journal indique la gravité de l’événement consigné.</span><span class="sxs-lookup"><span data-stu-id="f9d82-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="f9d82-138">Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f9d82-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f9d82-139">Créer des journaux dans la classe Programme</span><span class="sxs-lookup"><span data-stu-id="f9d82-139">Create logs in the Program class</span></span>

<span data-ttu-id="f9d82-140">Pour écrire des journaux dans la classe `Program` d’une application ASP.NET Core, récupérez une instance `ILogger` de l’injection de dépendances après la création de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="f9d82-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="f9d82-141">L’exploitation forestière pendant la construction de l’hôte n’est pas directement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="f9d82-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f9d82-142">Cependant, un bûcheron séparé peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="f9d82-142">However, a separate logger can be used.</span></span> <span data-ttu-id="f9d82-143">Dans l’exemple suivant, un bûcheron `CreateHostBuilder` [Serilog](https://serilog.net/) est utilisé pour se connecter .</span><span class="sxs-lookup"><span data-stu-id="f9d82-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="f9d82-144">`AddSerilog`utilise la configuration `Log.Logger`statique spécifiée dans :</span><span class="sxs-lookup"><span data-stu-id="f9d82-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="f9d82-145">Créer des journaux dans la classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="f9d82-145">Create logs in the Startup class</span></span>

<span data-ttu-id="f9d82-146">Pour écrire des journaux dans la méthode `Startup.Configure` d’une application ASP.NET Core, incluez un paramètre `ILogger` dans la signature de la méthode :</span><span class="sxs-lookup"><span data-stu-id="f9d82-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="f9d82-147">L’écriture de journaux avant la fin de l’installation du conteneur d’injection de dépendances dans la méthode `Startup.ConfigureServices` n’est pas prise en charge :</span><span class="sxs-lookup"><span data-stu-id="f9d82-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="f9d82-148">L’injection d’un enregistreur d’événements dans le constructeur `Startup` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="f9d82-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="f9d82-149">L’injection d’un enregistreur d’événements dans la signature de méthode `Startup.ConfigureServices` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="f9d82-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="f9d82-150">La raison de cette restriction est que la journalisation dépend de l’injection de dépendances et de la configuration qui, à son tour, dépend de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="f9d82-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="f9d82-151">Le conteneur d’injection de dépendances n’est pas configuré avant que `ConfigureServices` soit terminé.</span><span class="sxs-lookup"><span data-stu-id="f9d82-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="f9d82-152">L’injection de constructeur d’un enregistreur d’événements dans `Startup` fonctionne dans les versions antérieures d’ASP.NET Core, car un conteneur d’injection de dépendances distinct est créé pour l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="f9d82-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="f9d82-153">Pour plus d’informations sur la raison de la création d’un seul conteneur pour l’hôte générique, consultez l’[annonce de changement cassant](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="f9d82-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="f9d82-154">Si vous devez configurer un service qui dépend de `ILogger<T>`, vous pouvez toujours le faire à l’aide de l’injection de constructeur, ou avec une méthode de fabrique.</span><span class="sxs-lookup"><span data-stu-id="f9d82-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="f9d82-155">L’approche de la méthode de fabrique est recommandée uniquement s’il n’y a aucune autre option.</span><span class="sxs-lookup"><span data-stu-id="f9d82-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="f9d82-156">Supposons, par exemple, que vous deviez remplir une propriété avec un service à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="f9d82-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="f9d82-157">Le code en surbrillance précédent est une `Func` qui s’exécute la première fois que le conteneur d’injection de dépendances doit construire une instance de `MyService`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="f9d82-158">Vous pouvez accéder à tous les services inscrits de cette manière.</span><span class="sxs-lookup"><span data-stu-id="f9d82-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor-webassembly"></a><span data-ttu-id="f9d82-159">Créer des journaux dans Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f9d82-159">Create logs in Blazor WebAssembly</span></span>

<span data-ttu-id="f9d82-160">Configurez la connexion dans les applications WebAssembly De Blazor avec la `WebAssemblyHostBuilder.Logging` propriété dans `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f9d82-160">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="f9d82-161">La `Logging` propriété est <xref:Microsoft.Extensions.Logging.ILoggingBuilder>de type , de <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sorte que `Logging`toutes les méthodes d’extension disponibles sur sont également disponibles sur .</span><span class="sxs-lookup"><span data-stu-id="f9d82-161">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f9d82-162">Aucune méthode d’enregistreur d’événements asynchrone</span><span class="sxs-lookup"><span data-stu-id="f9d82-162">No asynchronous logger methods</span></span>

<span data-ttu-id="f9d82-163">La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="f9d82-163">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f9d82-164">Si votre magasin de données de journalisation est lent, n’écrivez pas directement dedans.</span><span class="sxs-lookup"><span data-stu-id="f9d82-164">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f9d82-165">Écrivez au départ les messages de journal dans un magasin rapide, puis déplacez-les dans le magasin lent.</span><span class="sxs-lookup"><span data-stu-id="f9d82-165">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f9d82-166">Par exemple, si vous vous connectez à SQL Server, évitez de le faire directement dans une méthode `Log`, car les méthodes `Log` sont synchrones.</span><span class="sxs-lookup"><span data-stu-id="f9d82-166">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f9d82-167">Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f9d82-167">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f9d82-168">Pour plus d’informations, voir [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) numéro GitHub.</span><span class="sxs-lookup"><span data-stu-id="f9d82-168">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="f9d82-169">Configuration</span><span class="sxs-lookup"><span data-stu-id="f9d82-169">Configuration</span></span>

<span data-ttu-id="f9d82-170">La configuration de fournisseur de journalisation est fournie par un ou plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="f9d82-170">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f9d82-171">Formats de fichiers (INI, JSON et XML).</span><span class="sxs-lookup"><span data-stu-id="f9d82-171">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f9d82-172">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="f9d82-172">Command-line arguments.</span></span>
* <span data-ttu-id="f9d82-173">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="f9d82-173">Environment variables.</span></span>
* <span data-ttu-id="f9d82-174">Objets .NET en mémoire.</span><span class="sxs-lookup"><span data-stu-id="f9d82-174">In-memory .NET objects.</span></span>
* <span data-ttu-id="f9d82-175">Stockage [Secret Manager](xref:security/app-secrets) non chiffré.</span><span class="sxs-lookup"><span data-stu-id="f9d82-175">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f9d82-176">Magasin utilisateur chiffré comme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f9d82-176">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f9d82-177">Fournisseurs personnalisés (installés ou créés).</span><span class="sxs-lookup"><span data-stu-id="f9d82-177">Custom providers (installed or created).</span></span>

<span data-ttu-id="f9d82-178">Par exemple, la configuration de journalisation est généralement fournie par la section `Logging` des fichiers de paramètres d’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-178">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f9d82-179">L’exemple suivant montre le contenu d’un fichier *appsettings.Development.json* standard :</span><span class="sxs-lookup"><span data-stu-id="f9d82-179">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="f9d82-180">La propriété `Logging` peut avoir un niveau `LogLevel` et des propriétés de module fournisseur d'informations (Console ici).</span><span class="sxs-lookup"><span data-stu-id="f9d82-180">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f9d82-181">La propriété `LogLevel` sous `Logging` spécifie le [niveau](#log-level) de journalisation minimal pour les catégories sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-181">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f9d82-182">Dans l’exemple, les catégories `System` et `Microsoft` sont consignées au niveau `Information`, et toutes les autres au niveau `Debug`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-182">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f9d82-183">Les autres propriétés sous `Logging` spécifient les fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-183">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f9d82-184">Cet exemple concerne le fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-184">The example is for the Console provider.</span></span> <span data-ttu-id="f9d82-185">Si un fournisseur prend `IncludeScopes` en charge les portées journal, indique s’ils sont [activés.](#log-scopes)</span><span class="sxs-lookup"><span data-stu-id="f9d82-185">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f9d82-186">Une propriété de fournisseur (comme `Console` dans l’exemple) peut également spécifier une propriété `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-186">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f9d82-187">`LogLevel` sous un fournisseur spécifie les niveaux à consigner pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-187">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f9d82-188">Si les niveaux sont spécifiés dans `Logging.{providername}.LogLevel`, ils remplacent ce qui est défini dans `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-188">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="f9d82-189">L’API de connexion n’inclut pas de scénario pour modifier les niveaux de journal pendant qu’une application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="f9d82-189">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f9d82-190">Cependant, certains fournisseurs de configuration sont capables de recharger la configuration, ce qui prend effet immédiatement sur la configuration de l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f9d82-190">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f9d82-191">Par exemple, le fournisseur de configuration `CreateDefaultBuilder` de [fichiers](xref:fundamentals/configuration/index#file-configuration-provider), qui est ajouté par pour lire les fichiers de paramètres, recharge la configuration de connexion par défaut.</span><span class="sxs-lookup"><span data-stu-id="f9d82-191">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f9d82-192">Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-192">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f9d82-193">Pour plus d’informations sur l’implémentation des fournisseurs de configuration, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f9d82-194">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="f9d82-194">Sample logging output</span></span>

<span data-ttu-id="f9d82-195">Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console lorsque l’application est exécutée en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="f9d82-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f9d82-196">Voici un exemple de sortie de la console :</span><span class="sxs-lookup"><span data-stu-id="f9d82-196">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="f9d82-197">Les journaux précédents ont été générés par une requête HTTP Get à l’exemple d’application à l’adresse `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f9d82-198">Voici un exemple des mêmes journaux tels qu’ils s’affichent dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="f9d82-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="f9d82-199">Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApiSample ».</span><span class="sxs-lookup"><span data-stu-id="f9d82-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="f9d82-200">Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9d82-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f9d82-201">ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="f9d82-202">Les autres sections de cet article détaillent certains points et présentent les options de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-202">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f9d82-203">Packages NuGet</span><span class="sxs-lookup"><span data-stu-id="f9d82-203">NuGet packages</span></span>

<span data-ttu-id="f9d82-204">Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="f9d82-204">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f9d82-205">Catégorie de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-205">Log category</span></span>

<span data-ttu-id="f9d82-206">Quand un objet `ILogger` est créé, une *catégorie* lui est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="f9d82-206">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f9d82-207">Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-207">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f9d82-208">Si la catégorie peut être n’importe quelle chaîne, la convention est d’utiliser le nom de la classe, par exemple « TodoApi.Controllers.TodoController ».</span><span class="sxs-lookup"><span data-stu-id="f9d82-208">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f9d82-209">Utilisez `ILogger<T>` pour obtenir une instance de `ILogger` qui utilise le nom de type complet `T` comme catégorie :</span><span class="sxs-lookup"><span data-stu-id="f9d82-209">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="f9d82-210">Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-210">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="f9d82-211">`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-211">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f9d82-212">Log level</span><span class="sxs-lookup"><span data-stu-id="f9d82-212">Log level</span></span>

<span data-ttu-id="f9d82-213">Chaque journal spécifie une valeur <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-213">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f9d82-214">Le niveau de journalisation indique la gravité ou l’importance.</span><span class="sxs-lookup"><span data-stu-id="f9d82-214">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f9d82-215">Vous pourriez par exemple écrire un journal `Information` lorsqu’une méthode se termine normalement et un journal `Warning` lorsqu’une méthode retourne un code de statut *404 Not Found*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-215">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f9d82-216">Le code suivant crée des journaux `Information` et `Warning` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-216">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f9d82-217">Dans le code précédent, le premier paramètre est [l’ID de l’événement de journal](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="f9d82-217">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f9d82-218">Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="f9d82-218">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f9d82-219">Les paramètres de méthode sont expliqués dans la section [Modèle de message](#log-message-template) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f9d82-219">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="f9d82-220">Les méthodes de journal qui comportent le niveau dans leur nom (par exemple, `LogInformation` et `LogWarning`) sont des [méthodes d’extension pour ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="f9d82-220">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f9d82-221">Elles appellent une méthode `Log` qui prend un paramètre `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-221">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f9d82-222">Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe.</span><span class="sxs-lookup"><span data-stu-id="f9d82-222">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f9d82-223">Pour plus d’informations, voir <xref:Microsoft.Extensions.Logging.ILogger> et le [code source des extensions d’enregistreur d'événements](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="f9d82-223">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f9d82-224">ASP.NET Core définit les niveaux de journalisation suivants, classés selon leur degré de gravité (du plus faible au plus élevé).</span><span class="sxs-lookup"><span data-stu-id="f9d82-224">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f9d82-225">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="f9d82-225">Trace = 0</span></span>

  <span data-ttu-id="f9d82-226">Informations en général exclusivement utiles à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-226">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f9d82-227">Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="f9d82-227">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f9d82-228">*Désactivé par défaut.*</span><span class="sxs-lookup"><span data-stu-id="f9d82-228">*Disabled by default.*</span></span>

* <span data-ttu-id="f9d82-229">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="f9d82-229">Debug = 1</span></span>

  <span data-ttu-id="f9d82-230">Informations qui peuvent être utiles dans le développement et le débogage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-230">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f9d82-231">Exemple : `Entering method Configure with flag set to true.` En raison de leur volume élevé, n’activez les journaux de niveau `Debug` en production que pour résoudre des problèmes.</span><span class="sxs-lookup"><span data-stu-id="f9d82-231">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f9d82-232">Information = 2</span><span class="sxs-lookup"><span data-stu-id="f9d82-232">Information = 2</span></span>

  <span data-ttu-id="f9d82-233">Informations de suivi du flux général de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-233">For tracking the general flow of the app.</span></span> <span data-ttu-id="f9d82-234">Ces journaux ont généralement une utilité à long terme.</span><span class="sxs-lookup"><span data-stu-id="f9d82-234">These logs typically have some long-term value.</span></span> <span data-ttu-id="f9d82-235">Exemple : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f9d82-235">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f9d82-236">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="f9d82-236">Warning = 3</span></span>

  <span data-ttu-id="f9d82-237">Informations sur les événements anormaux ou inattendus dans le flux de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-237">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f9d82-238">Il peut s’agir d’erreurs ou d’autres situations qui ne provoquent pas l’arrêt de l’application, mais qu’il peut être intéressant d’examiner.</span><span class="sxs-lookup"><span data-stu-id="f9d82-238">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f9d82-239">Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-239">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f9d82-240">Exemple : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f9d82-240">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f9d82-241">Error = 4</span><span class="sxs-lookup"><span data-stu-id="f9d82-241">Error = 4</span></span>

  <span data-ttu-id="f9d82-242">Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-242">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f9d82-243">Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-243">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f9d82-244">Exemple de message de journal : `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f9d82-244">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f9d82-245">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="f9d82-245">Critical = 5</span></span>

  <span data-ttu-id="f9d82-246">Fournit des informations sur des échecs qui nécessitent un examen immédiat.</span><span class="sxs-lookup"><span data-stu-id="f9d82-246">For failures that require immediate attention.</span></span> <span data-ttu-id="f9d82-247">Exemples : perte de données, espace disque insuffisant.</span><span class="sxs-lookup"><span data-stu-id="f9d82-247">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f9d82-248">Le niveau de journalisation permet de contrôler le volume de la sortie de journal écrite sur un support de stockage ou dans une fenêtre d’affichage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-248">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f9d82-249">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f9d82-249">For example:</span></span>

* <span data-ttu-id="f9d82-250">En production :</span><span class="sxs-lookup"><span data-stu-id="f9d82-250">In production:</span></span>
  * <span data-ttu-id="f9d82-251">L’enregistrement `Trace` aux `Information` niveaux à travers produit un volume élevé de messages journal détaillés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-251">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f9d82-252">Pour contrôler les coûts et ne `Trace` `Information` pas dépasser les limites de stockage de données, connectez-vous à des messages de niveau à un magasin de données à volume élevé et peu coûteux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-252">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f9d82-253">L’enregistrement `Warning` `Critical` à travers les niveaux produit généralement moins de messages journal plus petits.</span><span class="sxs-lookup"><span data-stu-id="f9d82-253">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f9d82-254">Par conséquent, les coûts et les limites de stockage ne sont généralement pas une préoccupation, ce qui se traduit par une plus grande flexibilité du choix des magasins de données.</span><span class="sxs-lookup"><span data-stu-id="f9d82-254">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f9d82-255">Pendant le développement :</span><span class="sxs-lookup"><span data-stu-id="f9d82-255">During development:</span></span>
  * <span data-ttu-id="f9d82-256">Connectez-vous `Warning` à travers `Critical` les messages à la console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-256">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f9d82-257">Ajoutez `Trace` `Information` des messages lors du dépannage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-257">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f9d82-258">La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-258">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f9d82-259">ASP.NET Core écrit des journaux pour les événements de framework.</span><span class="sxs-lookup"><span data-stu-id="f9d82-259">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f9d82-260">Aucun journal du niveau `Debug` ou `Trace` n’était créé dans les exemples de journaux présentés plus haut dans cet article, puisque les journaux au-dessous du niveau `Information` étaient exclus.</span><span class="sxs-lookup"><span data-stu-id="f9d82-260">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f9d82-261">Voici un exemple de journaux Console produits par l’exemple d’application configurée pour afficher les journaux `Debug` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-261">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="f9d82-262">ID d’événement de log</span><span class="sxs-lookup"><span data-stu-id="f9d82-262">Log event ID</span></span>

<span data-ttu-id="f9d82-263">Chaque journal peut spécifier un *ID d’événement*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-263">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f9d82-264">Pour cela, l’exemple d’application utilise une classe `LoggingEvents` définie localement :</span><span class="sxs-lookup"><span data-stu-id="f9d82-264">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="f9d82-265">Un ID d’événement associe un jeu d’événements.</span><span class="sxs-lookup"><span data-stu-id="f9d82-265">An event ID associates a set of events.</span></span> <span data-ttu-id="f9d82-266">Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.</span><span class="sxs-lookup"><span data-stu-id="f9d82-266">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f9d82-267">Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout.</span><span class="sxs-lookup"><span data-stu-id="f9d82-267">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f9d82-268">Le fournisseur Debug n’affiche pas les ID d’événements.</span><span class="sxs-lookup"><span data-stu-id="f9d82-268">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f9d82-269">Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :</span><span class="sxs-lookup"><span data-stu-id="f9d82-269">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f9d82-270">Modèle de message de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-270">Log message template</span></span>

<span data-ttu-id="f9d82-271">Chaque journal spécifie un modèle de message.</span><span class="sxs-lookup"><span data-stu-id="f9d82-271">Each log specifies a message template.</span></span> <span data-ttu-id="f9d82-272">Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis.</span><span class="sxs-lookup"><span data-stu-id="f9d82-272">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f9d82-273">Utilisez des noms et non des nombres pour les espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-273">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f9d82-274">L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-274">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f9d82-275">Dans le code suivant, on voit que les noms de paramètres sont hors séquence dans le modèle de message :</span><span class="sxs-lookup"><span data-stu-id="f9d82-275">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f9d82-276">Ce code crée un message de journal avec les valeurs des paramètres dans la séquence :</span><span class="sxs-lookup"><span data-stu-id="f9d82-276">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f9d82-277">Le framework de journalisation fonctionne ainsi pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f9d82-277">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f9d82-278">Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-278">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f9d82-279">C’est grâce à ces informations que les fournisseurs de journalisation peuvent stocker les valeurs des paramètres sous forme de champs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-279">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f9d82-280">Supposons par exemple que les appels de méthodes d’enregistreur d’événements se présentent ainsi :</span><span class="sxs-lookup"><span data-stu-id="f9d82-280">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f9d82-281">Si vous envoyez les journaux au Stockage Table Azure, chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journaux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-281">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f9d82-282">Une requête peut rechercher tous les journaux compris dans une plage `RequestTime` spécifique sans analyser le délai d’expiration du message texte.</span><span class="sxs-lookup"><span data-stu-id="f9d82-282">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f9d82-283">Journalisation des exceptions</span><span class="sxs-lookup"><span data-stu-id="f9d82-283">Logging exceptions</span></span>

<span data-ttu-id="f9d82-284">Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f9d82-284">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="f9d82-285">Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon.</span><span class="sxs-lookup"><span data-stu-id="f9d82-285">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f9d82-286">Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.</span><span class="sxs-lookup"><span data-stu-id="f9d82-286">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f9d82-287">Filtrage de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-287">Log filtering</span></span>

<span data-ttu-id="f9d82-288">Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories.</span><span class="sxs-lookup"><span data-stu-id="f9d82-288">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f9d82-289">Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-289">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f9d82-290">Pour supprimer tous les journaux, choisissez `LogLevel.None` comme niveau de journalisation minimal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-290">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f9d82-291">La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="f9d82-291">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f9d82-292">Créer des règles de filtre dans la configuration</span><span class="sxs-lookup"><span data-stu-id="f9d82-292">Create filter rules in configuration</span></span>

<span data-ttu-id="f9d82-293">Le code de `CreateDefaultBuilder` modèle de projet appelle à configurer la connexion pour les fournisseurs console, Debug et EventSource (ASP.NET Core 2.2 ou plus tard).</span><span class="sxs-lookup"><span data-stu-id="f9d82-293">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f9d82-294">La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, conformément à ce qui a été expliqué [plus haut dans cet article](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f9d82-294">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f9d82-295">Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f9d82-295">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="f9d82-296">Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une pour tous les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-296">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f9d82-297">Une seule règle est choisie pour chaque fournisseur à la création d’un objet `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-297">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f9d82-298">Règles de filtre dans le code</span><span class="sxs-lookup"><span data-stu-id="f9d82-298">Filter rules in code</span></span>

<span data-ttu-id="f9d82-299">L'exemple suivant montre comment enregistrer des règles de filtre dans le code :</span><span class="sxs-lookup"><span data-stu-id="f9d82-299">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="f9d82-300">Le second `AddFilter` spécifie le fournisseur Debug par son nom de type.</span><span class="sxs-lookup"><span data-stu-id="f9d82-300">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f9d82-301">Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-301">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f9d82-302">Mode d’application des règles de filtre</span><span class="sxs-lookup"><span data-stu-id="f9d82-302">How filtering rules are applied</span></span>

<span data-ttu-id="f9d82-303">Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="f9d82-303">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f9d82-304">Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="f9d82-304">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f9d82-305">Number</span><span class="sxs-lookup"><span data-stu-id="f9d82-305">Number</span></span> | <span data-ttu-id="f9d82-306">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="f9d82-306">Provider</span></span>      | <span data-ttu-id="f9d82-307">Catégories commençant par...</span><span class="sxs-lookup"><span data-stu-id="f9d82-307">Categories that begin with ...</span></span>          | <span data-ttu-id="f9d82-308">Niveau de journalisation minimum</span><span class="sxs-lookup"><span data-stu-id="f9d82-308">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f9d82-309">1</span><span class="sxs-lookup"><span data-stu-id="f9d82-309">1</span></span>      | <span data-ttu-id="f9d82-310">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-310">Debug</span></span>         | <span data-ttu-id="f9d82-311">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="f9d82-311">All categories</span></span>                          | <span data-ttu-id="f9d82-312">Information</span><span class="sxs-lookup"><span data-stu-id="f9d82-312">Information</span></span>       |
| <span data-ttu-id="f9d82-313">2</span><span class="sxs-lookup"><span data-stu-id="f9d82-313">2</span></span>      | <span data-ttu-id="f9d82-314">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-314">Console</span></span>       | <span data-ttu-id="f9d82-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="f9d82-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f9d82-316">Avertissement</span><span class="sxs-lookup"><span data-stu-id="f9d82-316">Warning</span></span>           |
| <span data-ttu-id="f9d82-317">3</span><span class="sxs-lookup"><span data-stu-id="f9d82-317">3</span></span>      | <span data-ttu-id="f9d82-318">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-318">Console</span></span>       | <span data-ttu-id="f9d82-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="f9d82-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f9d82-320">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-320">Debug</span></span>             |
| <span data-ttu-id="f9d82-321">4</span><span class="sxs-lookup"><span data-stu-id="f9d82-321">4</span></span>      | <span data-ttu-id="f9d82-322">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-322">Console</span></span>       | <span data-ttu-id="f9d82-323">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="f9d82-323">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f9d82-324">Error</span><span class="sxs-lookup"><span data-stu-id="f9d82-324">Error</span></span>             |
| <span data-ttu-id="f9d82-325">5</span><span class="sxs-lookup"><span data-stu-id="f9d82-325">5</span></span>      | <span data-ttu-id="f9d82-326">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-326">Console</span></span>       | <span data-ttu-id="f9d82-327">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="f9d82-327">All categories</span></span>                          | <span data-ttu-id="f9d82-328">Information</span><span class="sxs-lookup"><span data-stu-id="f9d82-328">Information</span></span>       |
| <span data-ttu-id="f9d82-329">6</span><span class="sxs-lookup"><span data-stu-id="f9d82-329">6</span></span>      | <span data-ttu-id="f9d82-330">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="f9d82-330">All providers</span></span> | <span data-ttu-id="f9d82-331">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="f9d82-331">All categories</span></span>                          | <span data-ttu-id="f9d82-332">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-332">Debug</span></span>             |
| <span data-ttu-id="f9d82-333">7</span><span class="sxs-lookup"><span data-stu-id="f9d82-333">7</span></span>      | <span data-ttu-id="f9d82-334">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="f9d82-334">All providers</span></span> | <span data-ttu-id="f9d82-335">Système</span><span class="sxs-lookup"><span data-stu-id="f9d82-335">System</span></span>                                  | <span data-ttu-id="f9d82-336">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-336">Debug</span></span>             |
| <span data-ttu-id="f9d82-337">8</span><span class="sxs-lookup"><span data-stu-id="f9d82-337">8</span></span>      | <span data-ttu-id="f9d82-338">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-338">Debug</span></span>         | <span data-ttu-id="f9d82-339">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f9d82-339">Microsoft</span></span>                               | <span data-ttu-id="f9d82-340">Trace</span><span class="sxs-lookup"><span data-stu-id="f9d82-340">Trace</span></span>             |

<span data-ttu-id="f9d82-341">À la création d’un objet `ILogger`, l’objet `ILoggerFactory` sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-341">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f9d82-342">Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-342">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f9d82-343">La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.</span><span class="sxs-lookup"><span data-stu-id="f9d82-343">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f9d82-344">L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :</span><span class="sxs-lookup"><span data-stu-id="f9d82-344">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f9d82-345">Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias.</span><span class="sxs-lookup"><span data-stu-id="f9d82-345">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f9d82-346">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.</span><span class="sxs-lookup"><span data-stu-id="f9d82-346">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f9d82-347">À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant.</span><span class="sxs-lookup"><span data-stu-id="f9d82-347">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f9d82-348">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.</span><span class="sxs-lookup"><span data-stu-id="f9d82-348">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f9d82-349">Si plusieurs règles sont sélectionnées, prenez la **dernière**.</span><span class="sxs-lookup"><span data-stu-id="f9d82-349">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f9d82-350">Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-350">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f9d82-351">Avec la liste de règles précédente, supposons que vous créez un objet `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :</span><span class="sxs-lookup"><span data-stu-id="f9d82-351">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f9d82-352">Les règles 1, 6 et 8 s’appliquent au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="f9d82-352">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f9d82-353">La règle 8 est sélectionnée, car c’est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="f9d82-353">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f9d82-354">Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-354">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f9d82-355">La règle 3 est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="f9d82-355">Rule 3 is most specific.</span></span>

<span data-ttu-id="f9d82-356">L’instance `ILogger` ainsi produite envoie des journaux de niveau `Trace` ou supérieur au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="f9d82-356">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f9d82-357">Les journaux de niveau `Debug` et supérieurs sont envoyés au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-357">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f9d82-358">Alias de fournisseur</span><span class="sxs-lookup"><span data-stu-id="f9d82-358">Provider aliases</span></span>

<span data-ttu-id="f9d82-359">Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="f9d82-359">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f9d82-360">Pour les fournisseurs intégrés, utilisez les alias suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-360">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f9d82-361">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-361">Console</span></span>
* <span data-ttu-id="f9d82-362">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-362">Debug</span></span>
* <span data-ttu-id="f9d82-363">EventSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-363">EventSource</span></span>
* <span data-ttu-id="f9d82-364">EventLog</span><span class="sxs-lookup"><span data-stu-id="f9d82-364">EventLog</span></span>
* <span data-ttu-id="f9d82-365">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-365">TraceSource</span></span>
* <span data-ttu-id="f9d82-366">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f9d82-366">AzureAppServicesFile</span></span>
* <span data-ttu-id="f9d82-367">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f9d82-367">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f9d82-368">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f9d82-368">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f9d82-369">Niveau minimum par défaut</span><span class="sxs-lookup"><span data-stu-id="f9d82-369">Default minimum level</span></span>

<span data-ttu-id="f9d82-370">Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique.</span><span class="sxs-lookup"><span data-stu-id="f9d82-370">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f9d82-371">L’exemple suivant montre comment définir le niveau minimum :</span><span class="sxs-lookup"><span data-stu-id="f9d82-371">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="f9d82-372">Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-372">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f9d82-373">Fonctions de filtrage</span><span class="sxs-lookup"><span data-stu-id="f9d82-373">Filter functions</span></span>

<span data-ttu-id="f9d82-374">Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle.</span><span class="sxs-lookup"><span data-stu-id="f9d82-374">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f9d82-375">Le code de la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-375">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f9d82-376">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f9d82-376">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="f9d82-377">Niveaux et les catégories de système</span><span class="sxs-lookup"><span data-stu-id="f9d82-377">System categories and levels</span></span>

<span data-ttu-id="f9d82-378">Voici quelques catégories utilisées par ASP.NET Core et Entity Framework Core, avec des notes sur les journaux associés :</span><span class="sxs-lookup"><span data-stu-id="f9d82-378">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f9d82-379">Category</span><span class="sxs-lookup"><span data-stu-id="f9d82-379">Category</span></span>                            | <span data-ttu-id="f9d82-380">Notes</span><span class="sxs-lookup"><span data-stu-id="f9d82-380">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f9d82-381">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f9d82-381">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f9d82-382">Diagnostics ASP.NET Core généraux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-382">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f9d82-383">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="f9d82-383">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f9d82-384">Liste des clés considérées, trouvées et utilisées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-384">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f9d82-385">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f9d82-385">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f9d82-386">Hôtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-386">Hosts allowed.</span></span> |
| <span data-ttu-id="f9d82-387">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="f9d82-387">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f9d82-388">Temps de traitement des requêtes HTTP et heure de démarrage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-388">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f9d82-389">Liste des assemblys de démarrage d’hébergement chargés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-389">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f9d82-390">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="f9d82-390">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f9d82-391">Diagnostics MVC et Razor.</span><span class="sxs-lookup"><span data-stu-id="f9d82-391">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f9d82-392">Liaison de données, exécution de filtres, compilation de vues, sélection d’actions.</span><span class="sxs-lookup"><span data-stu-id="f9d82-392">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f9d82-393">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="f9d82-393">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f9d82-394">Informations de correspondance des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="f9d82-394">Route matching information.</span></span> |
| <span data-ttu-id="f9d82-395">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="f9d82-395">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f9d82-396">Démarrage et arrêt de la connexion, et réponses persistantes.</span><span class="sxs-lookup"><span data-stu-id="f9d82-396">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f9d82-397">Informations du certificat HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9d82-397">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f9d82-398">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f9d82-398">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f9d82-399">Fichiers pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f9d82-399">Files served.</span></span> |
| <span data-ttu-id="f9d82-400">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f9d82-400">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f9d82-401">Diagnostics Entity Framework Core généraux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-401">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f9d82-402">Activité et configuration de la base de données, détection des modifications, migrations.</span><span class="sxs-lookup"><span data-stu-id="f9d82-402">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f9d82-403">Étendues de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-403">Log scopes</span></span>

 <span data-ttu-id="f9d82-404">Une *étendue* peut regrouper un ensemble d’opérations logiques.</span><span class="sxs-lookup"><span data-stu-id="f9d82-404">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f9d82-405">Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble.</span><span class="sxs-lookup"><span data-stu-id="f9d82-405">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f9d82-406">Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.</span><span class="sxs-lookup"><span data-stu-id="f9d82-406">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f9d82-407">Une étendue est un type `IDisposable` qui est retourné par la méthode <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>. Elle s’applique tant qu’elle n’est pas supprimée.</span><span class="sxs-lookup"><span data-stu-id="f9d82-407">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f9d82-408">Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-408">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="f9d82-409">Le code suivant active les étendues pour le fournisseur Console :</span><span class="sxs-lookup"><span data-stu-id="f9d82-409">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f9d82-410">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9d82-410">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="f9d82-411">La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues.</span><span class="sxs-lookup"><span data-stu-id="f9d82-411">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f9d82-412">Pour plus d'informations sur la configuration, reportez-vous à la section [Configuration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f9d82-412">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f9d82-413">Chaque message de journal fournit les informations incluses dans l’étendue :</span><span class="sxs-lookup"><span data-stu-id="f9d82-413">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f9d82-414">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="f9d82-414">Built-in logging providers</span></span>

<span data-ttu-id="f9d82-415">ASP.NET Core contient les fournisseurs suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-415">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f9d82-416">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-416">Console</span></span>](#console-provider)
* [<span data-ttu-id="f9d82-417">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-417">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f9d82-418">EventSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-418">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f9d82-419">EventLog</span><span class="sxs-lookup"><span data-stu-id="f9d82-419">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f9d82-420">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-420">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f9d82-421">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f9d82-421">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f9d82-422">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f9d82-422">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f9d82-423">ApplicationsInsights</span><span class="sxs-lookup"><span data-stu-id="f9d82-423">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f9d82-424">Pour plus d’informations sur stdout et sur la journalisation du débogage avec le module ASP.NET Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-424">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f9d82-425">Fournisseur Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-425">Console provider</span></span>

<span data-ttu-id="f9d82-426">Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-426">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f9d82-427">Pour voir la sortie de la journalisation Console, ouvrez une invite de commandes dans le dossier du projet et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f9d82-427">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f9d82-428">Fournisseur Debug</span><span class="sxs-lookup"><span data-stu-id="f9d82-428">Debug provider</span></span>

<span data-ttu-id="f9d82-429">Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (appels de la méthode `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="f9d82-429">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f9d82-430">Sur Linux, ce fournisseur écrit la sortie de log dans */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-430">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f9d82-431">Fournisseur de source d’événements</span><span class="sxs-lookup"><span data-stu-id="f9d82-431">Event Source provider</span></span>

<span data-ttu-id="f9d82-432">Le forfait [fournisseur Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) écrit à une plateforme `Microsoft-Extensions-Logging`transversale Event Source avec le nom .</span><span class="sxs-lookup"><span data-stu-id="f9d82-432">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f9d82-433">Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="f9d82-433">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f9d82-434">Le fournisseur Event Source `CreateDefaultBuilder` est ajouté automatiquement lorsqu’il est appelé pour construire l’hôte.</span><span class="sxs-lookup"><span data-stu-id="f9d82-434">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="f9d82-435">outil de trace de dotnet</span><span class="sxs-lookup"><span data-stu-id="f9d82-435">dotnet trace tooling</span></span>

<span data-ttu-id="f9d82-436">[L’outil de trace de dotnet](/dotnet/core/diagnostics/dotnet-trace) est un outil mondial CLI multiplateforme qui permet la collecte de traces .NET Core d’un processus en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="f9d82-436">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="f9d82-437">L’outil <xref:Microsoft.Extensions.Logging.EventSource> recueille les <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>données du fournisseur à l’aide d’un .</span><span class="sxs-lookup"><span data-stu-id="f9d82-437">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="f9d82-438">Installez l’outillage de trace de dotnet avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f9d82-438">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="f9d82-439">Utilisez l’outillage de trace dotnet pour recueillir une trace d’une application :</span><span class="sxs-lookup"><span data-stu-id="f9d82-439">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="f9d82-440">Si l’application ne construit `CreateDefaultBuilder`pas l’hôte avec , ajouter le [fournisseur Event Source](#event-source-provider) à la configuration de connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-440">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="f9d82-441">Exécutez l’application avec la `dotnet run` commande.</span><span class="sxs-lookup"><span data-stu-id="f9d82-441">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="f9d82-442">Déterminer l’identifiant de processus (PID) de l’application .NET Core :</span><span class="sxs-lookup"><span data-stu-id="f9d82-442">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="f9d82-443">Sur Windows, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="f9d82-443">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="f9d82-444">Gestionnaire de tâches (Ctrl-Alt-Del)</span><span class="sxs-lookup"><span data-stu-id="f9d82-444">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="f9d82-445">commande tasklist</span><span class="sxs-lookup"><span data-stu-id="f9d82-445">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="f9d82-446">Get-Process Powershell commande</span><span class="sxs-lookup"><span data-stu-id="f9d82-446">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="f9d82-447">Sur Linux, utilisez la [commande pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="f9d82-447">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="f9d82-448">Trouvez la MIP pour le processus qui porte le même nom que l’assemblage de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-448">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="f9d82-449">Exécutez `dotnet trace` la commande.</span><span class="sxs-lookup"><span data-stu-id="f9d82-449">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="f9d82-450">Syntaxe de commande générale :</span><span class="sxs-lookup"><span data-stu-id="f9d82-450">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="f9d82-451">Lors de l’utilisation d’une coque `--providers` de commande`'`PowerShell, enfermez la valeur en citations simples ( ):</span><span class="sxs-lookup"><span data-stu-id="f9d82-451">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="f9d82-452">Sur les plates-formes `-f speedscope` non Windows, ajoutez la possibilité `speedscope`de modifier le format du fichier de trace de sortie à .</span><span class="sxs-lookup"><span data-stu-id="f9d82-452">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="f9d82-453">Mot clé</span><span class="sxs-lookup"><span data-stu-id="f9d82-453">Keyword</span></span> | <span data-ttu-id="f9d82-454">Description</span><span class="sxs-lookup"><span data-stu-id="f9d82-454">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="f9d82-455">1</span><span class="sxs-lookup"><span data-stu-id="f9d82-455">1</span></span>       | <span data-ttu-id="f9d82-456">Log méta événements `LoggingEventSource`sur le .</span><span class="sxs-lookup"><span data-stu-id="f9d82-456">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="f9d82-457">Ne enregistre pas `ILogger`les événements de ).</span><span class="sxs-lookup"><span data-stu-id="f9d82-457">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="f9d82-458">2</span><span class="sxs-lookup"><span data-stu-id="f9d82-458">2</span></span>       | <span data-ttu-id="f9d82-459">Allume `Message` l’événement `ILogger.Log()` lorsqu’il est appelé.</span><span class="sxs-lookup"><span data-stu-id="f9d82-459">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f9d82-460">Fournit de l’information de façon programmatique (non formatée).</span><span class="sxs-lookup"><span data-stu-id="f9d82-460">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="f9d82-461">4</span><span class="sxs-lookup"><span data-stu-id="f9d82-461">4</span></span>       | <span data-ttu-id="f9d82-462">Allume `FormatMessage` l’événement `ILogger.Log()` lorsqu’il est appelé.</span><span class="sxs-lookup"><span data-stu-id="f9d82-462">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f9d82-463">Fournit la version de chaîne formatée de l’information.</span><span class="sxs-lookup"><span data-stu-id="f9d82-463">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="f9d82-464">8</span><span class="sxs-lookup"><span data-stu-id="f9d82-464">8</span></span>       | <span data-ttu-id="f9d82-465">Allume `MessageJson` l’événement `ILogger.Log()` lorsqu’il est appelé.</span><span class="sxs-lookup"><span data-stu-id="f9d82-465">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="f9d82-466">Fournit une représentation JSON des arguments.</span><span class="sxs-lookup"><span data-stu-id="f9d82-466">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="f9d82-467">Niveau d'événement</span><span class="sxs-lookup"><span data-stu-id="f9d82-467">Event Level</span></span> | <span data-ttu-id="f9d82-468">Description</span><span class="sxs-lookup"><span data-stu-id="f9d82-468">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="f9d82-469">0</span><span class="sxs-lookup"><span data-stu-id="f9d82-469">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="f9d82-470">1</span><span class="sxs-lookup"><span data-stu-id="f9d82-470">1</span></span>           | `Critical`      |
   | <span data-ttu-id="f9d82-471">2</span><span class="sxs-lookup"><span data-stu-id="f9d82-471">2</span></span>           | `Error`         |
   | <span data-ttu-id="f9d82-472">3</span><span class="sxs-lookup"><span data-stu-id="f9d82-472">3</span></span>           | `Warning`       |
   | <span data-ttu-id="f9d82-473">4</span><span class="sxs-lookup"><span data-stu-id="f9d82-473">4</span></span>           | `Informational` |
   | <span data-ttu-id="f9d82-474">5</span><span class="sxs-lookup"><span data-stu-id="f9d82-474">5</span></span>           | `Verbose`       |

   <span data-ttu-id="f9d82-475">`FilterSpecs`entrées `{Logger Category}` et `{Event Level}` représentent des conditions supplémentaires de filtrage des journaux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-475">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="f9d82-476">Entrées `FilterSpecs` séparées avec un`;`point-virgule ( ).</span><span class="sxs-lookup"><span data-stu-id="f9d82-476">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="f9d82-477">Exemple à l’aide d’une `--providers` coque de commande Windows **(pas** de citations uniques autour de la valeur) :</span><span class="sxs-lookup"><span data-stu-id="f9d82-477">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="f9d82-478">La commande précédente active :</span><span class="sxs-lookup"><span data-stu-id="f9d82-478">The preceding command activates:</span></span>

   * <span data-ttu-id="f9d82-479">L’enregistreur Event Source pour produire`4`des cordes`2`formatées ( ) pour les erreurs ( ).</span><span class="sxs-lookup"><span data-stu-id="f9d82-479">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="f9d82-480">`Microsoft.AspNetCore.Hosting`l’exploitation `Informational` forestière au`4`niveau de l’exploitation forestière ( ).</span><span class="sxs-lookup"><span data-stu-id="f9d82-480">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="f9d82-481">Arrêtez l’outillage de trace de dotnet en appuyant sur la clé Enter ou Ctrl-C.</span><span class="sxs-lookup"><span data-stu-id="f9d82-481">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="f9d82-482">La trace est enregistrée avec le nom *trace.nettrace* dans le dossier où la `dotnet trace` commande est exécutée.</span><span class="sxs-lookup"><span data-stu-id="f9d82-482">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="f9d82-483">Ouvrez la trace avec [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="f9d82-483">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="f9d82-484">Ouvrez le fichier *trace.nettrace* et explorez les traces.</span><span class="sxs-lookup"><span data-stu-id="f9d82-484">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="f9d82-485">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="f9d82-485">For more information, see:</span></span>

* <span data-ttu-id="f9d82-486">[Trace pour l’utilité d’analyse de performance (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span><span class="sxs-lookup"><span data-stu-id="f9d82-486">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="f9d82-487">[Trace pour l’utilité d’analyse de performance (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics documentation de dépôt GitHub)</span><span class="sxs-lookup"><span data-stu-id="f9d82-487">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="f9d82-488">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span><span class="sxs-lookup"><span data-stu-id="f9d82-488">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="f9d82-489">[LoggingEventSource source de référence (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Pour obtenir la source `release/{Version}`de `{Version}` référence pour une version différente, changer la branche à , où est la version de ASP.NET Core souhaité.</span><span class="sxs-lookup"><span data-stu-id="f9d82-489">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="f9d82-490">[Perfview](#perfview) &ndash; Utile pour voir les traces event Source.</span><span class="sxs-lookup"><span data-stu-id="f9d82-490">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="f9d82-491">Perfview (Perfview)</span><span class="sxs-lookup"><span data-stu-id="f9d82-491">Perfview</span></span>

<span data-ttu-id="f9d82-492">Utilisez [l’utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-492">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f9d82-493">Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9d82-493">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f9d82-494">Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**.</span><span class="sxs-lookup"><span data-stu-id="f9d82-494">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f9d82-495">(N’oubliez pas d’inclure l’astérisque au début de la chaîne.)</span><span class="sxs-lookup"><span data-stu-id="f9d82-495">(Don't miss the asterisk at the start of the string.)</span></span>

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f9d82-497">Fournisseur EventLog Windows</span><span class="sxs-lookup"><span data-stu-id="f9d82-497">Windows EventLog provider</span></span>

<span data-ttu-id="f9d82-498">Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="f9d82-498">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f9d82-499">Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) vous permettent de passer à <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-499">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f9d82-500">Si `null` elles sont spécifiées ou non, les paramètres par défaut suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="f9d82-500">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f9d82-501">`LogName`&ndash; "Application"</span><span class="sxs-lookup"><span data-stu-id="f9d82-501">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="f9d82-502">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="f9d82-502">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="f9d82-503">`MachineName` &ndash; ordinateur local</span><span class="sxs-lookup"><span data-stu-id="f9d82-503">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="f9d82-504">Les événements sont enregistrés pour [le niveau d’avertissement et plus élevé](#log-level).</span><span class="sxs-lookup"><span data-stu-id="f9d82-504">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="f9d82-505">Pour enregistrer les `Warning`événements inférieurs à, définissez explicitement le niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-505">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="f9d82-506">Par exemple, ajoutez ce qui suit au fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="f9d82-506">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="f9d82-507">Fournisseur TraceSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-507">TraceSource provider</span></span>

<span data-ttu-id="f9d82-508">Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-508">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f9d82-509">Les [surcharges AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) vous permettent de passer un commutateur de source et un écouteur de suivi.</span><span class="sxs-lookup"><span data-stu-id="f9d82-509">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f9d82-510">Pour pouvoir utiliser ce fournisseur, il faut que l’application s’exécute sur .NET Framework (au lieu de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="f9d82-510">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f9d82-511">Le fournisseur peut acheminer les messages vers différents [détecteurs d’événements](/dotnet/framework/debug-trace-profile/trace-listeners), comme <xref:System.Diagnostics.TextWriterTraceListener> (utilisé dans l’exemple d’application).</span><span class="sxs-lookup"><span data-stu-id="f9d82-511">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f9d82-512">Fournisseur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f9d82-512">Azure App Service provider</span></span>

<span data-ttu-id="f9d82-513">Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="f9d82-513">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="f9d82-514">Le fournisseur de package n’est pas inclus dans le framework partagé.</span><span class="sxs-lookup"><span data-stu-id="f9d82-514">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="f9d82-515">Pour utiliser le fournisseur, ajoutez le package du fournisseur au projet.</span><span class="sxs-lookup"><span data-stu-id="f9d82-515">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="f9d82-516">Pour configurer les paramètres du fournisseur, utilisez <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> et <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f9d82-516">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="f9d82-517">En cas de déploiement sur une application App Service, celle-ci applique les paramètres définis dans la section [Journaux App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="f9d82-517">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f9d82-518">Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-518">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f9d82-519">**Journalisation des applications (système de fichiers)**</span><span class="sxs-lookup"><span data-stu-id="f9d82-519">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f9d82-520">**Journalisation des applications (objet blob)**</span><span class="sxs-lookup"><span data-stu-id="f9d82-520">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f9d82-521">L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-521">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f9d82-522">La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2.</span><span class="sxs-lookup"><span data-stu-id="f9d82-522">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f9d82-523">Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-523">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f9d82-524">Le fournisseur fonctionne uniquement quand le projet s’exécute dans l’environnement Azure.</span><span class="sxs-lookup"><span data-stu-id="f9d82-524">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f9d82-525">Il n’a aucun effet si le projet s’exécute localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.</span><span class="sxs-lookup"><span data-stu-id="f9d82-525">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f9d82-526">Streaming des journaux Azure</span><span class="sxs-lookup"><span data-stu-id="f9d82-526">Azure log streaming</span></span>

<span data-ttu-id="f9d82-527">La diffusion en continu des journaux Azure permet d’afficher l’activité de journalisation en temps réel aux emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-527">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f9d82-528">Serveur d'applications</span><span class="sxs-lookup"><span data-stu-id="f9d82-528">The app server</span></span>
* <span data-ttu-id="f9d82-529">Serveur web</span><span class="sxs-lookup"><span data-stu-id="f9d82-529">The web server</span></span>
* <span data-ttu-id="f9d82-530">Suivi des demandes ayant échoué</span><span class="sxs-lookup"><span data-stu-id="f9d82-530">Failed request tracing</span></span>

<span data-ttu-id="f9d82-531">Pour configurer le streaming des journaux Azure :</span><span class="sxs-lookup"><span data-stu-id="f9d82-531">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f9d82-532">Accédez à la page **Journaux App Service** dans le portail de votre application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-532">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f9d82-533">Définissez **Journal des applications (Système de fichiers)** sur **Activé**.</span><span class="sxs-lookup"><span data-stu-id="f9d82-533">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f9d82-534">Choisissez le **niveau** du journal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-534">Choose the log **Level**.</span></span> <span data-ttu-id="f9d82-535">Ce paramètre ne s’applique qu’au streaming en rondins Azure, et non aux autres fournisseurs de connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-535">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f9d82-536">Accédez à la page **Streaming des journaux** pour voir les messages d’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-536">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f9d82-537">Ils sont consignés par application par le biais de l’interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-537">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f9d82-538">Journalisation des traces Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f9d82-538">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f9d82-539">Le package de fournisseur [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit des journaux dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-539">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f9d82-540">Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie.</span><span class="sxs-lookup"><span data-stu-id="f9d82-540">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f9d82-541">Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-541">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f9d82-542">Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9d82-542">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f9d82-543">Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-543">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f9d82-544">N’utilisez pas le [package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;destiné à ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="f9d82-544">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f9d82-545">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="f9d82-545">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f9d82-546">Vue d'ensemble d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="f9d82-546">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f9d82-547">[Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-547">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f9d82-548">[Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-548">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f9d82-549">[Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="f9d82-549">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f9d82-550">[Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="f9d82-550">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f9d82-551">Fournisseurs de journalisation tiers</span><span class="sxs-lookup"><span data-stu-id="f9d82-551">Third-party logging providers</span></span>

<span data-ttu-id="f9d82-552">Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f9d82-552">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f9d82-553">[elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-553">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f9d82-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f9d82-555">[JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="f9d82-555">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f9d82-556">[KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="f9d82-556">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f9d82-557">[Log4Net](https://logging.apache.org/log4net/) ([repo GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="f9d82-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f9d82-558">[Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-558">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f9d82-559">[NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-559">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f9d82-560">[Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f9d82-560">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f9d82-561">[Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="f9d82-561">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f9d82-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f9d82-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f9d82-563">Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f9d82-563">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f9d82-564">L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :</span><span class="sxs-lookup"><span data-stu-id="f9d82-564">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f9d82-565">Ajoutez un package NuGet à votre projet.</span><span class="sxs-lookup"><span data-stu-id="f9d82-565">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f9d82-566">Appelez `ILoggerFactory` une méthode d’extension fournie par le cadre d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f9d82-566">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f9d82-567">Pour plus d’informations, consultez la documentation de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-567">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f9d82-568">Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f9d82-568">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9d82-569">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f9d82-569">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f9d82-570">Par [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f9d82-570">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f9d82-571">.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-571">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f9d82-572">Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-572">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="f9d82-573">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9d82-573">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="f9d82-574">Ajouter des fournisseurs</span><span class="sxs-lookup"><span data-stu-id="f9d82-574">Add providers</span></span>

<span data-ttu-id="f9d82-575">Un fournisseur de journalisation affiche ou stocke des journaux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-575">A logging provider displays or stores logs.</span></span> <span data-ttu-id="f9d82-576">Par exemple, le fournisseur Console affiche les journaux dans la console, et le fournisseur Azure Application Insights les stocke dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-576">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="f9d82-577">Il est possible d’envoyer les journaux à plusieurs endroits en ajoutant plusieurs fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-577">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="f9d82-578">Pour ajouter un fournisseur, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="f9d82-578">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="f9d82-579">Le code précédent nécessite des références à `Microsoft.Extensions.Logging` et `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-579">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="f9d82-580">Le modèle de projet par défaut appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-580">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="f9d82-581">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-581">Console</span></span>
* <span data-ttu-id="f9d82-582">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-582">Debug</span></span>
* <span data-ttu-id="f9d82-583">EventSource (à partir d’ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="f9d82-583">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="f9d82-584">Si vous utilisez `CreateDefaultBuilder`, vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix.</span><span class="sxs-lookup"><span data-stu-id="f9d82-584">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="f9d82-585">Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="f9d82-585">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="f9d82-586">Vous trouverez des informations sur les [fournisseurs de journalisation intégrés](#built-in-logging-providers) et les [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f9d82-586">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="f9d82-587">Créer des journaux</span><span class="sxs-lookup"><span data-stu-id="f9d82-587">Create logs</span></span>

<span data-ttu-id="f9d82-588">Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-588">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="f9d82-589">Dans une application web ou un service hébergé, obtenez un `ILogger` à partir de l’injection de dépendances (DI).</span><span class="sxs-lookup"><span data-stu-id="f9d82-589">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="f9d82-590">Dans les applications de console non hôtes, utilisez le `LoggerFactory` pour créer un `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-590">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="f9d82-591">L’exemple d’ASP.NET Core suivant crée un enregistreur d’événements de catégorie `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-591">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="f9d82-592">La *catégorie* du journal est une chaîne associée à chaque journal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-592">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="f9d82-593">L’instance `ILogger<T>` fournie par l’injection de dépendances crée des journaux ayant comme catégorie un nom complet de type `T`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-593">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="f9d82-594">Dans les exemples d’ASP.NET Core et d’application de console suivants, l’enregistreur d’événements est utilisé pour créer des journaux de niveau `Information`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-594">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="f9d82-595">Le *niveau* du journal indique la gravité de l’événement consigné.</span><span class="sxs-lookup"><span data-stu-id="f9d82-595">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="f9d82-596">Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f9d82-596">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="f9d82-597">Créer des journaux au démarrage</span><span class="sxs-lookup"><span data-stu-id="f9d82-597">Create logs in Startup</span></span>

<span data-ttu-id="f9d82-598">Pour écrire des journaux dans la classe `Startup`, ajoutez un paramètre `ILogger` dans la signature de constructeur :</span><span class="sxs-lookup"><span data-stu-id="f9d82-598">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="f9d82-599">Créer des journaux dans la classe Programme</span><span class="sxs-lookup"><span data-stu-id="f9d82-599">Create logs in the Program class</span></span>

<span data-ttu-id="f9d82-600">Pour écrire des journaux dans la classe `Program`, récupérez une instance `ILogger` auprès de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="f9d82-600">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="f9d82-601">L’exploitation forestière pendant la construction de l’hôte n’est pas directement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="f9d82-601">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="f9d82-602">Cependant, un bûcheron séparé peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="f9d82-602">However, a separate logger can be used.</span></span> <span data-ttu-id="f9d82-603">Dans l’exemple suivant, un bûcheron `CreateWebHostBuilder` [Serilog](https://serilog.net/) est utilisé pour se connecter .</span><span class="sxs-lookup"><span data-stu-id="f9d82-603">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="f9d82-604">`AddSerilog`utilise la configuration `Log.Logger`statique spécifiée dans :</span><span class="sxs-lookup"><span data-stu-id="f9d82-604">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="f9d82-605">Aucune méthode d’enregistreur d’événements asynchrone</span><span class="sxs-lookup"><span data-stu-id="f9d82-605">No asynchronous logger methods</span></span>

<span data-ttu-id="f9d82-606">La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="f9d82-606">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="f9d82-607">Si votre magasin de données de journalisation est lent, n’écrivez pas directement dedans.</span><span class="sxs-lookup"><span data-stu-id="f9d82-607">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="f9d82-608">Écrivez au départ les messages de journal dans un magasin rapide, puis déplacez-les dans le magasin lent.</span><span class="sxs-lookup"><span data-stu-id="f9d82-608">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="f9d82-609">Par exemple, si vous vous connectez à SQL Server, évitez de le faire directement dans une méthode `Log`, car les méthodes `Log` sont synchrones.</span><span class="sxs-lookup"><span data-stu-id="f9d82-609">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="f9d82-610">Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f9d82-610">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="f9d82-611">Pour plus d’informations, voir [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) numéro GitHub.</span><span class="sxs-lookup"><span data-stu-id="f9d82-611">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="f9d82-612">Configuration</span><span class="sxs-lookup"><span data-stu-id="f9d82-612">Configuration</span></span>

<span data-ttu-id="f9d82-613">La configuration de fournisseur de journalisation est fournie par un ou plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="f9d82-613">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="f9d82-614">Formats de fichiers (INI, JSON et XML).</span><span class="sxs-lookup"><span data-stu-id="f9d82-614">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f9d82-615">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="f9d82-615">Command-line arguments.</span></span>
* <span data-ttu-id="f9d82-616">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="f9d82-616">Environment variables.</span></span>
* <span data-ttu-id="f9d82-617">Objets .NET en mémoire.</span><span class="sxs-lookup"><span data-stu-id="f9d82-617">In-memory .NET objects.</span></span>
* <span data-ttu-id="f9d82-618">Stockage [Secret Manager](xref:security/app-secrets) non chiffré.</span><span class="sxs-lookup"><span data-stu-id="f9d82-618">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f9d82-619">Magasin utilisateur chiffré comme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f9d82-619">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f9d82-620">Fournisseurs personnalisés (installés ou créés).</span><span class="sxs-lookup"><span data-stu-id="f9d82-620">Custom providers (installed or created).</span></span>

<span data-ttu-id="f9d82-621">Par exemple, la configuration de journalisation est généralement fournie par la section `Logging` des fichiers de paramètres d’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-621">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="f9d82-622">L’exemple suivant montre le contenu d’un fichier *appsettings.Development.json* standard :</span><span class="sxs-lookup"><span data-stu-id="f9d82-622">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="f9d82-623">La propriété `Logging` peut avoir un niveau `LogLevel` et des propriétés de module fournisseur d'informations (Console ici).</span><span class="sxs-lookup"><span data-stu-id="f9d82-623">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="f9d82-624">La propriété `LogLevel` sous `Logging` spécifie le [niveau](#log-level) de journalisation minimal pour les catégories sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-624">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="f9d82-625">Dans l’exemple, les catégories `System` et `Microsoft` sont consignées au niveau `Information`, et toutes les autres au niveau `Debug`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-625">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="f9d82-626">Les autres propriétés sous `Logging` spécifient les fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-626">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="f9d82-627">Cet exemple concerne le fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-627">The example is for the Console provider.</span></span> <span data-ttu-id="f9d82-628">Si un fournisseur prend `IncludeScopes` en charge les portées journal, indique s’ils sont [activés.](#log-scopes)</span><span class="sxs-lookup"><span data-stu-id="f9d82-628">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="f9d82-629">Une propriété de fournisseur (comme `Console` dans l’exemple) peut également spécifier une propriété `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-629">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="f9d82-630">`LogLevel` sous un fournisseur spécifie les niveaux à consigner pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-630">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="f9d82-631">Si les niveaux sont spécifiés dans `Logging.{providername}.LogLevel`, ils remplacent ce qui est défini dans `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-631">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="f9d82-632">L’API de connexion n’inclut pas de scénario pour modifier les niveaux de journal pendant qu’une application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="f9d82-632">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="f9d82-633">Cependant, certains fournisseurs de configuration sont capables de recharger la configuration, ce qui prend effet immédiatement sur la configuration de l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f9d82-633">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="f9d82-634">Par exemple, le fournisseur de configuration `CreateDefaultBuilder` de [fichiers](xref:fundamentals/configuration/index#file-configuration-provider), qui est ajouté par pour lire les fichiers de paramètres, recharge la configuration de connexion par défaut.</span><span class="sxs-lookup"><span data-stu-id="f9d82-634">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="f9d82-635">Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-635">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="f9d82-636">Pour plus d’informations sur l’implémentation des fournisseurs de configuration, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-636">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="f9d82-637">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="f9d82-637">Sample logging output</span></span>

<span data-ttu-id="f9d82-638">Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console lorsque l’application est exécutée en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="f9d82-638">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="f9d82-639">Voici un exemple de sortie de la console :</span><span class="sxs-lookup"><span data-stu-id="f9d82-639">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="f9d82-640">Les journaux précédents ont été générés par une requête HTTP Get à l’exemple d’application à l’adresse `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-640">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="f9d82-641">Voici un exemple des mêmes journaux tels qu’ils s’affichent dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="f9d82-641">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="f9d82-642">Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApi ».</span><span class="sxs-lookup"><span data-stu-id="f9d82-642">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="f9d82-643">Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9d82-643">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="f9d82-644">ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-644">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="f9d82-645">Les autres sections de cet article détaillent certains points et présentent les options de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-645">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="f9d82-646">Packages NuGet</span><span class="sxs-lookup"><span data-stu-id="f9d82-646">NuGet packages</span></span>

<span data-ttu-id="f9d82-647">Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="f9d82-647">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="f9d82-648">Catégorie de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-648">Log category</span></span>

<span data-ttu-id="f9d82-649">Quand un objet `ILogger` est créé, une *catégorie* lui est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="f9d82-649">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="f9d82-650">Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-650">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="f9d82-651">Si la catégorie peut être n’importe quelle chaîne, la convention est d’utiliser le nom de la classe, par exemple « TodoApi.Controllers.TodoController ».</span><span class="sxs-lookup"><span data-stu-id="f9d82-651">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="f9d82-652">Utilisez `ILogger<T>` pour obtenir une instance de `ILogger` qui utilise le nom de type complet `T` comme catégorie :</span><span class="sxs-lookup"><span data-stu-id="f9d82-652">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="f9d82-653">Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-653">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="f9d82-654">`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-654">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="f9d82-655">Log level</span><span class="sxs-lookup"><span data-stu-id="f9d82-655">Log level</span></span>

<span data-ttu-id="f9d82-656">Chaque journal spécifie une valeur <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-656">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="f9d82-657">Le niveau de journalisation indique la gravité ou l’importance.</span><span class="sxs-lookup"><span data-stu-id="f9d82-657">The log level indicates the severity or importance.</span></span> <span data-ttu-id="f9d82-658">Vous pourriez par exemple écrire un journal `Information` lorsqu’une méthode se termine normalement et un journal `Warning` lorsqu’une méthode retourne un code de statut *404 Not Found*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-658">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="f9d82-659">Le code suivant crée des journaux `Information` et `Warning` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-659">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f9d82-660">Dans le code précédent, le premier paramètre est [l’ID de l’événement de journal](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="f9d82-660">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="f9d82-661">Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="f9d82-661">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="f9d82-662">Les paramètres de méthode sont expliqués dans la section [Modèle de message](#log-message-template) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="f9d82-662">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="f9d82-663">Les méthodes de journal qui comportent le niveau dans leur nom (par exemple, `LogInformation` et `LogWarning`) sont des [méthodes d’extension pour ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="f9d82-663">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="f9d82-664">Elles appellent une méthode `Log` qui prend un paramètre `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-664">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="f9d82-665">Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe.</span><span class="sxs-lookup"><span data-stu-id="f9d82-665">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="f9d82-666">Pour plus d’informations, voir <xref:Microsoft.Extensions.Logging.ILogger> et le [code source des extensions d’enregistreur d'événements](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="f9d82-666">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="f9d82-667">ASP.NET Core définit les niveaux de journalisation suivants, classés selon leur degré de gravité (du plus faible au plus élevé).</span><span class="sxs-lookup"><span data-stu-id="f9d82-667">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="f9d82-668">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="f9d82-668">Trace = 0</span></span>

  <span data-ttu-id="f9d82-669">Informations en général exclusivement utiles à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-669">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="f9d82-670">Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="f9d82-670">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="f9d82-671">*Désactivé par défaut.*</span><span class="sxs-lookup"><span data-stu-id="f9d82-671">*Disabled by default.*</span></span>

* <span data-ttu-id="f9d82-672">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="f9d82-672">Debug = 1</span></span>

  <span data-ttu-id="f9d82-673">Informations qui peuvent être utiles dans le développement et le débogage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-673">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="f9d82-674">Exemple : `Entering method Configure with flag set to true.` En raison de leur volume élevé, n’activez les journaux de niveau `Debug` en production que pour résoudre des problèmes.</span><span class="sxs-lookup"><span data-stu-id="f9d82-674">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="f9d82-675">Information = 2</span><span class="sxs-lookup"><span data-stu-id="f9d82-675">Information = 2</span></span>

  <span data-ttu-id="f9d82-676">Informations de suivi du flux général de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-676">For tracking the general flow of the app.</span></span> <span data-ttu-id="f9d82-677">Ces journaux ont généralement une utilité à long terme.</span><span class="sxs-lookup"><span data-stu-id="f9d82-677">These logs typically have some long-term value.</span></span> <span data-ttu-id="f9d82-678">Exemple : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="f9d82-678">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="f9d82-679">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="f9d82-679">Warning = 3</span></span>

  <span data-ttu-id="f9d82-680">Informations sur les événements anormaux ou inattendus dans le flux de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-680">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="f9d82-681">Il peut s’agir d’erreurs ou d’autres situations qui ne provoquent pas l’arrêt de l’application, mais qu’il peut être intéressant d’examiner.</span><span class="sxs-lookup"><span data-stu-id="f9d82-681">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="f9d82-682">Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-682">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="f9d82-683">Exemple : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="f9d82-683">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="f9d82-684">Error = 4</span><span class="sxs-lookup"><span data-stu-id="f9d82-684">Error = 4</span></span>

  <span data-ttu-id="f9d82-685">Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-685">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="f9d82-686">Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-686">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="f9d82-687">Exemple de message de journal : `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="f9d82-687">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="f9d82-688">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="f9d82-688">Critical = 5</span></span>

  <span data-ttu-id="f9d82-689">Fournit des informations sur des échecs qui nécessitent un examen immédiat.</span><span class="sxs-lookup"><span data-stu-id="f9d82-689">For failures that require immediate attention.</span></span> <span data-ttu-id="f9d82-690">Exemples : perte de données, espace disque insuffisant.</span><span class="sxs-lookup"><span data-stu-id="f9d82-690">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="f9d82-691">Le niveau de journalisation permet de contrôler le volume de la sortie de journal écrite sur un support de stockage ou dans une fenêtre d’affichage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-691">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="f9d82-692">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f9d82-692">For example:</span></span>

* <span data-ttu-id="f9d82-693">En production :</span><span class="sxs-lookup"><span data-stu-id="f9d82-693">In production:</span></span>
  * <span data-ttu-id="f9d82-694">L’enregistrement `Trace` aux `Information` niveaux à travers produit un volume élevé de messages journal détaillés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-694">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="f9d82-695">Pour contrôler les coûts et ne `Trace` `Information` pas dépasser les limites de stockage de données, connectez-vous à des messages de niveau à un magasin de données à volume élevé et peu coûteux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-695">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="f9d82-696">L’enregistrement `Warning` `Critical` à travers les niveaux produit généralement moins de messages journal plus petits.</span><span class="sxs-lookup"><span data-stu-id="f9d82-696">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="f9d82-697">Par conséquent, les coûts et les limites de stockage ne sont généralement pas une préoccupation, ce qui se traduit par une plus grande flexibilité du choix des magasins de données.</span><span class="sxs-lookup"><span data-stu-id="f9d82-697">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="f9d82-698">Pendant le développement :</span><span class="sxs-lookup"><span data-stu-id="f9d82-698">During development:</span></span>
  * <span data-ttu-id="f9d82-699">Connectez-vous `Warning` à travers `Critical` les messages à la console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-699">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="f9d82-700">Ajoutez `Trace` `Information` des messages lors du dépannage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-700">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="f9d82-701">La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-701">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="f9d82-702">ASP.NET Core écrit des journaux pour les événements de framework.</span><span class="sxs-lookup"><span data-stu-id="f9d82-702">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="f9d82-703">Aucun journal du niveau `Debug` ou `Trace` n’était créé dans les exemples de journaux présentés plus haut dans cet article, puisque les journaux au-dessous du niveau `Information` étaient exclus.</span><span class="sxs-lookup"><span data-stu-id="f9d82-703">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="f9d82-704">Voici un exemple de journaux Console produits par l’exemple d’application configurée pour afficher les journaux `Debug` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-704">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="f9d82-705">ID d’événement de log</span><span class="sxs-lookup"><span data-stu-id="f9d82-705">Log event ID</span></span>

<span data-ttu-id="f9d82-706">Chaque journal peut spécifier un *ID d’événement*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-706">Each log can specify an *event ID*.</span></span> <span data-ttu-id="f9d82-707">Pour cela, l’exemple d’application utilise une classe `LoggingEvents` définie localement :</span><span class="sxs-lookup"><span data-stu-id="f9d82-707">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="f9d82-708">Un ID d’événement associe un jeu d’événements.</span><span class="sxs-lookup"><span data-stu-id="f9d82-708">An event ID associates a set of events.</span></span> <span data-ttu-id="f9d82-709">Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.</span><span class="sxs-lookup"><span data-stu-id="f9d82-709">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="f9d82-710">Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout.</span><span class="sxs-lookup"><span data-stu-id="f9d82-710">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="f9d82-711">Le fournisseur Debug n’affiche pas les ID d’événements.</span><span class="sxs-lookup"><span data-stu-id="f9d82-711">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="f9d82-712">Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :</span><span class="sxs-lookup"><span data-stu-id="f9d82-712">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="f9d82-713">Modèle de message de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-713">Log message template</span></span>

<span data-ttu-id="f9d82-714">Chaque journal spécifie un modèle de message.</span><span class="sxs-lookup"><span data-stu-id="f9d82-714">Each log specifies a message template.</span></span> <span data-ttu-id="f9d82-715">Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis.</span><span class="sxs-lookup"><span data-stu-id="f9d82-715">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="f9d82-716">Utilisez des noms et non des nombres pour les espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-716">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="f9d82-717">L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-717">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="f9d82-718">Dans le code suivant, on voit que les noms de paramètres sont hors séquence dans le modèle de message :</span><span class="sxs-lookup"><span data-stu-id="f9d82-718">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="f9d82-719">Ce code crée un message de journal avec les valeurs des paramètres dans la séquence :</span><span class="sxs-lookup"><span data-stu-id="f9d82-719">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="f9d82-720">Le framework de journalisation fonctionne ainsi pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f9d82-720">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="f9d82-721">Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-721">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="f9d82-722">C’est grâce à ces informations que les fournisseurs de journalisation peuvent stocker les valeurs des paramètres sous forme de champs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-722">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="f9d82-723">Supposons par exemple que les appels de méthodes d’enregistreur d’événements se présentent ainsi :</span><span class="sxs-lookup"><span data-stu-id="f9d82-723">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="f9d82-724">Si vous envoyez les journaux au Stockage Table Azure, chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journaux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-724">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="f9d82-725">Une requête peut rechercher tous les journaux compris dans une plage `RequestTime` spécifique sans analyser le délai d’expiration du message texte.</span><span class="sxs-lookup"><span data-stu-id="f9d82-725">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="f9d82-726">Journalisation des exceptions</span><span class="sxs-lookup"><span data-stu-id="f9d82-726">Logging exceptions</span></span>

<span data-ttu-id="f9d82-727">Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f9d82-727">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="f9d82-728">Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon.</span><span class="sxs-lookup"><span data-stu-id="f9d82-728">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="f9d82-729">Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.</span><span class="sxs-lookup"><span data-stu-id="f9d82-729">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="f9d82-730">Filtrage de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-730">Log filtering</span></span>

<span data-ttu-id="f9d82-731">Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories.</span><span class="sxs-lookup"><span data-stu-id="f9d82-731">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="f9d82-732">Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-732">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="f9d82-733">Pour supprimer tous les journaux, choisissez `LogLevel.None` comme niveau de journalisation minimal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-733">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="f9d82-734">La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="f9d82-734">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="f9d82-735">Créer des règles de filtre dans la configuration</span><span class="sxs-lookup"><span data-stu-id="f9d82-735">Create filter rules in configuration</span></span>

<span data-ttu-id="f9d82-736">Le code de `CreateDefaultBuilder` modèle de projet appelle à configurer la connexion pour les fournisseurs console, Debug et EventSource (ASP.NET Core 2.2 ou plus tard).</span><span class="sxs-lookup"><span data-stu-id="f9d82-736">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="f9d82-737">La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, conformément à ce qui a été expliqué [plus haut dans cet article](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f9d82-737">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="f9d82-738">Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f9d82-738">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="f9d82-739">Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une pour tous les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="f9d82-739">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="f9d82-740">Une seule règle est choisie pour chaque fournisseur à la création d’un objet `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-740">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="f9d82-741">Règles de filtre dans le code</span><span class="sxs-lookup"><span data-stu-id="f9d82-741">Filter rules in code</span></span>

<span data-ttu-id="f9d82-742">L'exemple suivant montre comment enregistrer des règles de filtre dans le code :</span><span class="sxs-lookup"><span data-stu-id="f9d82-742">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="f9d82-743">Le second `AddFilter` spécifie le fournisseur Debug par son nom de type.</span><span class="sxs-lookup"><span data-stu-id="f9d82-743">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="f9d82-744">Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-744">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="f9d82-745">Mode d’application des règles de filtre</span><span class="sxs-lookup"><span data-stu-id="f9d82-745">How filtering rules are applied</span></span>

<span data-ttu-id="f9d82-746">Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="f9d82-746">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="f9d82-747">Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="f9d82-747">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="f9d82-748">Number</span><span class="sxs-lookup"><span data-stu-id="f9d82-748">Number</span></span> | <span data-ttu-id="f9d82-749">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="f9d82-749">Provider</span></span>      | <span data-ttu-id="f9d82-750">Catégories commençant par...</span><span class="sxs-lookup"><span data-stu-id="f9d82-750">Categories that begin with ...</span></span>          | <span data-ttu-id="f9d82-751">Niveau de journalisation minimum</span><span class="sxs-lookup"><span data-stu-id="f9d82-751">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="f9d82-752">1</span><span class="sxs-lookup"><span data-stu-id="f9d82-752">1</span></span>      | <span data-ttu-id="f9d82-753">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-753">Debug</span></span>         | <span data-ttu-id="f9d82-754">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="f9d82-754">All categories</span></span>                          | <span data-ttu-id="f9d82-755">Information</span><span class="sxs-lookup"><span data-stu-id="f9d82-755">Information</span></span>       |
| <span data-ttu-id="f9d82-756">2</span><span class="sxs-lookup"><span data-stu-id="f9d82-756">2</span></span>      | <span data-ttu-id="f9d82-757">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-757">Console</span></span>       | <span data-ttu-id="f9d82-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="f9d82-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="f9d82-759">Avertissement</span><span class="sxs-lookup"><span data-stu-id="f9d82-759">Warning</span></span>           |
| <span data-ttu-id="f9d82-760">3</span><span class="sxs-lookup"><span data-stu-id="f9d82-760">3</span></span>      | <span data-ttu-id="f9d82-761">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-761">Console</span></span>       | <span data-ttu-id="f9d82-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="f9d82-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="f9d82-763">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-763">Debug</span></span>             |
| <span data-ttu-id="f9d82-764">4</span><span class="sxs-lookup"><span data-stu-id="f9d82-764">4</span></span>      | <span data-ttu-id="f9d82-765">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-765">Console</span></span>       | <span data-ttu-id="f9d82-766">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="f9d82-766">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="f9d82-767">Error</span><span class="sxs-lookup"><span data-stu-id="f9d82-767">Error</span></span>             |
| <span data-ttu-id="f9d82-768">5</span><span class="sxs-lookup"><span data-stu-id="f9d82-768">5</span></span>      | <span data-ttu-id="f9d82-769">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-769">Console</span></span>       | <span data-ttu-id="f9d82-770">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="f9d82-770">All categories</span></span>                          | <span data-ttu-id="f9d82-771">Information</span><span class="sxs-lookup"><span data-stu-id="f9d82-771">Information</span></span>       |
| <span data-ttu-id="f9d82-772">6</span><span class="sxs-lookup"><span data-stu-id="f9d82-772">6</span></span>      | <span data-ttu-id="f9d82-773">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="f9d82-773">All providers</span></span> | <span data-ttu-id="f9d82-774">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="f9d82-774">All categories</span></span>                          | <span data-ttu-id="f9d82-775">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-775">Debug</span></span>             |
| <span data-ttu-id="f9d82-776">7</span><span class="sxs-lookup"><span data-stu-id="f9d82-776">7</span></span>      | <span data-ttu-id="f9d82-777">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="f9d82-777">All providers</span></span> | <span data-ttu-id="f9d82-778">Système</span><span class="sxs-lookup"><span data-stu-id="f9d82-778">System</span></span>                                  | <span data-ttu-id="f9d82-779">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-779">Debug</span></span>             |
| <span data-ttu-id="f9d82-780">8</span><span class="sxs-lookup"><span data-stu-id="f9d82-780">8</span></span>      | <span data-ttu-id="f9d82-781">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-781">Debug</span></span>         | <span data-ttu-id="f9d82-782">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f9d82-782">Microsoft</span></span>                               | <span data-ttu-id="f9d82-783">Trace</span><span class="sxs-lookup"><span data-stu-id="f9d82-783">Trace</span></span>             |

<span data-ttu-id="f9d82-784">À la création d’un objet `ILogger`, l’objet `ILoggerFactory` sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-784">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="f9d82-785">Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-785">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="f9d82-786">La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.</span><span class="sxs-lookup"><span data-stu-id="f9d82-786">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="f9d82-787">L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :</span><span class="sxs-lookup"><span data-stu-id="f9d82-787">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="f9d82-788">Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias.</span><span class="sxs-lookup"><span data-stu-id="f9d82-788">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="f9d82-789">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.</span><span class="sxs-lookup"><span data-stu-id="f9d82-789">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="f9d82-790">À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant.</span><span class="sxs-lookup"><span data-stu-id="f9d82-790">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="f9d82-791">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.</span><span class="sxs-lookup"><span data-stu-id="f9d82-791">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="f9d82-792">Si plusieurs règles sont sélectionnées, prenez la **dernière**.</span><span class="sxs-lookup"><span data-stu-id="f9d82-792">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="f9d82-793">Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-793">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="f9d82-794">Avec la liste de règles précédente, supposons que vous créez un objet `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :</span><span class="sxs-lookup"><span data-stu-id="f9d82-794">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="f9d82-795">Les règles 1, 6 et 8 s’appliquent au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="f9d82-795">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="f9d82-796">La règle 8 est sélectionnée, car c’est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="f9d82-796">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="f9d82-797">Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-797">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="f9d82-798">La règle 3 est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="f9d82-798">Rule 3 is most specific.</span></span>

<span data-ttu-id="f9d82-799">L’instance `ILogger` ainsi produite envoie des journaux de niveau `Trace` ou supérieur au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="f9d82-799">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="f9d82-800">Les journaux de niveau `Debug` et supérieurs sont envoyés au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-800">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="f9d82-801">Alias de fournisseur</span><span class="sxs-lookup"><span data-stu-id="f9d82-801">Provider aliases</span></span>

<span data-ttu-id="f9d82-802">Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="f9d82-802">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="f9d82-803">Pour les fournisseurs intégrés, utilisez les alias suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-803">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="f9d82-804">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-804">Console</span></span>
* <span data-ttu-id="f9d82-805">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-805">Debug</span></span>
* <span data-ttu-id="f9d82-806">EventSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-806">EventSource</span></span>
* <span data-ttu-id="f9d82-807">EventLog</span><span class="sxs-lookup"><span data-stu-id="f9d82-807">EventLog</span></span>
* <span data-ttu-id="f9d82-808">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-808">TraceSource</span></span>
* <span data-ttu-id="f9d82-809">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f9d82-809">AzureAppServicesFile</span></span>
* <span data-ttu-id="f9d82-810">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f9d82-810">AzureAppServicesBlob</span></span>
* <span data-ttu-id="f9d82-811">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="f9d82-811">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="f9d82-812">Niveau minimum par défaut</span><span class="sxs-lookup"><span data-stu-id="f9d82-812">Default minimum level</span></span>

<span data-ttu-id="f9d82-813">Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique.</span><span class="sxs-lookup"><span data-stu-id="f9d82-813">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="f9d82-814">L’exemple suivant montre comment définir le niveau minimum :</span><span class="sxs-lookup"><span data-stu-id="f9d82-814">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="f9d82-815">Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-815">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="f9d82-816">Fonctions de filtrage</span><span class="sxs-lookup"><span data-stu-id="f9d82-816">Filter functions</span></span>

<span data-ttu-id="f9d82-817">Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle.</span><span class="sxs-lookup"><span data-stu-id="f9d82-817">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="f9d82-818">Le code de la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-818">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="f9d82-819">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f9d82-819">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="f9d82-820">Niveaux et les catégories de système</span><span class="sxs-lookup"><span data-stu-id="f9d82-820">System categories and levels</span></span>

<span data-ttu-id="f9d82-821">Voici quelques catégories utilisées par ASP.NET Core et Entity Framework Core, avec des notes sur les journaux associés :</span><span class="sxs-lookup"><span data-stu-id="f9d82-821">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="f9d82-822">Category</span><span class="sxs-lookup"><span data-stu-id="f9d82-822">Category</span></span>                            | <span data-ttu-id="f9d82-823">Notes</span><span class="sxs-lookup"><span data-stu-id="f9d82-823">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="f9d82-824">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="f9d82-824">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="f9d82-825">Diagnostics ASP.NET Core généraux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-825">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="f9d82-826">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="f9d82-826">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="f9d82-827">Liste des clés considérées, trouvées et utilisées.</span><span class="sxs-lookup"><span data-stu-id="f9d82-827">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="f9d82-828">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="f9d82-828">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="f9d82-829">Hôtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-829">Hosts allowed.</span></span> |
| <span data-ttu-id="f9d82-830">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="f9d82-830">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="f9d82-831">Temps de traitement des requêtes HTTP et heure de démarrage.</span><span class="sxs-lookup"><span data-stu-id="f9d82-831">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="f9d82-832">Liste des assemblys de démarrage d’hébergement chargés.</span><span class="sxs-lookup"><span data-stu-id="f9d82-832">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="f9d82-833">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="f9d82-833">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="f9d82-834">Diagnostics MVC et Razor.</span><span class="sxs-lookup"><span data-stu-id="f9d82-834">MVC and Razor diagnostics.</span></span> <span data-ttu-id="f9d82-835">Liaison de données, exécution de filtres, compilation de vues, sélection d’actions.</span><span class="sxs-lookup"><span data-stu-id="f9d82-835">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="f9d82-836">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="f9d82-836">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="f9d82-837">Informations de correspondance des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="f9d82-837">Route matching information.</span></span> |
| <span data-ttu-id="f9d82-838">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="f9d82-838">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="f9d82-839">Démarrage et arrêt de la connexion, et réponses persistantes.</span><span class="sxs-lookup"><span data-stu-id="f9d82-839">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="f9d82-840">Informations du certificat HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9d82-840">HTTPS certificate information.</span></span> |
| <span data-ttu-id="f9d82-841">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="f9d82-841">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="f9d82-842">Fichiers pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f9d82-842">Files served.</span></span> |
| <span data-ttu-id="f9d82-843">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f9d82-843">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="f9d82-844">Diagnostics Entity Framework Core généraux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-844">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="f9d82-845">Activité et configuration de la base de données, détection des modifications, migrations.</span><span class="sxs-lookup"><span data-stu-id="f9d82-845">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="f9d82-846">Étendues de journal</span><span class="sxs-lookup"><span data-stu-id="f9d82-846">Log scopes</span></span>

 <span data-ttu-id="f9d82-847">Une *étendue* peut regrouper un ensemble d’opérations logiques.</span><span class="sxs-lookup"><span data-stu-id="f9d82-847">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="f9d82-848">Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble.</span><span class="sxs-lookup"><span data-stu-id="f9d82-848">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="f9d82-849">Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.</span><span class="sxs-lookup"><span data-stu-id="f9d82-849">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="f9d82-850">Une étendue est un type `IDisposable` qui est retourné par la méthode <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>. Elle s’applique tant qu’elle n’est pas supprimée.</span><span class="sxs-lookup"><span data-stu-id="f9d82-850">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="f9d82-851">Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :</span><span class="sxs-lookup"><span data-stu-id="f9d82-851">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="f9d82-852">Le code suivant active les étendues pour le fournisseur Console :</span><span class="sxs-lookup"><span data-stu-id="f9d82-852">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="f9d82-853">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9d82-853">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="f9d82-854">La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues.</span><span class="sxs-lookup"><span data-stu-id="f9d82-854">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="f9d82-855">Pour plus d'informations sur la configuration, reportez-vous à la section [Configuration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="f9d82-855">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="f9d82-856">Chaque message de journal fournit les informations incluses dans l’étendue :</span><span class="sxs-lookup"><span data-stu-id="f9d82-856">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="f9d82-857">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="f9d82-857">Built-in logging providers</span></span>

<span data-ttu-id="f9d82-858">ASP.NET Core contient les fournisseurs suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-858">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="f9d82-859">Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-859">Console</span></span>](#console-provider)
* [<span data-ttu-id="f9d82-860">Débogage</span><span class="sxs-lookup"><span data-stu-id="f9d82-860">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="f9d82-861">EventSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-861">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="f9d82-862">EventLog</span><span class="sxs-lookup"><span data-stu-id="f9d82-862">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="f9d82-863">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-863">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="f9d82-864">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="f9d82-864">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f9d82-865">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="f9d82-865">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="f9d82-866">ApplicationsInsights</span><span class="sxs-lookup"><span data-stu-id="f9d82-866">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="f9d82-867">Pour plus d’informations sur stdout et sur la journalisation du débogage avec le module ASP.NET Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-867">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="f9d82-868">Fournisseur Console</span><span class="sxs-lookup"><span data-stu-id="f9d82-868">Console provider</span></span>

<span data-ttu-id="f9d82-869">Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console.</span><span class="sxs-lookup"><span data-stu-id="f9d82-869">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="f9d82-870">Pour voir la sortie de la journalisation Console, ouvrez une invite de commandes dans le dossier du projet et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f9d82-870">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="f9d82-871">Fournisseur Debug</span><span class="sxs-lookup"><span data-stu-id="f9d82-871">Debug provider</span></span>

<span data-ttu-id="f9d82-872">Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (appels de la méthode `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="f9d82-872">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="f9d82-873">Sur Linux, ce fournisseur écrit la sortie de log dans */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-873">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="f9d82-874">Fournisseur de source d’événements</span><span class="sxs-lookup"><span data-stu-id="f9d82-874">Event Source provider</span></span>

<span data-ttu-id="f9d82-875">Le forfait [fournisseur Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) écrit à une plateforme `Microsoft-Extensions-Logging`transversale Event Source avec le nom .</span><span class="sxs-lookup"><span data-stu-id="f9d82-875">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="f9d82-876">Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="f9d82-876">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="f9d82-877">Le fournisseur Event Source `CreateDefaultBuilder` est ajouté automatiquement lorsqu’il est appelé pour construire l’hôte.</span><span class="sxs-lookup"><span data-stu-id="f9d82-877">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="f9d82-878">Utilisez [l’utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux.</span><span class="sxs-lookup"><span data-stu-id="f9d82-878">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="f9d82-879">Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9d82-879">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="f9d82-880">Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**.</span><span class="sxs-lookup"><span data-stu-id="f9d82-880">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="f9d82-881">(N’oubliez pas d’inclure l’astérisque au début de la chaîne.)</span><span class="sxs-lookup"><span data-stu-id="f9d82-881">(Don't miss the asterisk at the start of the string.)</span></span>

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="f9d82-883">Fournisseur EventLog Windows</span><span class="sxs-lookup"><span data-stu-id="f9d82-883">Windows EventLog provider</span></span>

<span data-ttu-id="f9d82-884">Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="f9d82-884">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="f9d82-885">Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) vous permettent de passer à <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-885">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="f9d82-886">Si `null` elles sont spécifiées ou non, les paramètres par défaut suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="f9d82-886">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="f9d82-887">`LogName`&ndash; "Application"</span><span class="sxs-lookup"><span data-stu-id="f9d82-887">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="f9d82-888">`SourceName`&ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="f9d82-888">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="f9d82-889">`MachineName` &ndash; ordinateur local</span><span class="sxs-lookup"><span data-stu-id="f9d82-889">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="f9d82-890">Les événements sont enregistrés pour [le niveau d’avertissement et plus élevé](#log-level).</span><span class="sxs-lookup"><span data-stu-id="f9d82-890">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="f9d82-891">Pour enregistrer les `Warning`événements inférieurs à, définissez explicitement le niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-891">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="f9d82-892">Par exemple, ajoutez ce qui suit au fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="f9d82-892">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="f9d82-893">Fournisseur TraceSource</span><span class="sxs-lookup"><span data-stu-id="f9d82-893">TraceSource provider</span></span>

<span data-ttu-id="f9d82-894">Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-894">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="f9d82-895">Les [surcharges AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) vous permettent de passer un commutateur de source et un écouteur de suivi.</span><span class="sxs-lookup"><span data-stu-id="f9d82-895">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="f9d82-896">Pour pouvoir utiliser ce fournisseur, il faut que l’application s’exécute sur .NET Framework (au lieu de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="f9d82-896">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="f9d82-897">Le fournisseur peut acheminer les messages vers différents [détecteurs d’événements](/dotnet/framework/debug-trace-profile/trace-listeners), comme <xref:System.Diagnostics.TextWriterTraceListener> (utilisé dans l’exemple d’application).</span><span class="sxs-lookup"><span data-stu-id="f9d82-897">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="f9d82-898">Fournisseur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f9d82-898">Azure App Service provider</span></span>

<span data-ttu-id="f9d82-899">Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="f9d82-899">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="f9d82-900">Le package du fournisseur n’est pas inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f9d82-900">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f9d82-901">Lorsque vous ciblez .NET framework ou référencez le métapackage `Microsoft.AspNetCore.App`, ajoutez le package de fournisseur dans le projet.</span><span class="sxs-lookup"><span data-stu-id="f9d82-901">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="f9d82-902">Une surcharge <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permet de transmettre <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="f9d82-902">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="f9d82-903">L’objet de paramètres peut remplacer les paramètres par défaut, comme le modèle de sortie de journalisation, le nom d’objet blob et la limite de taille de fichier.</span><span class="sxs-lookup"><span data-stu-id="f9d82-903">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="f9d82-904">(*Modèle de sortie* est un modèle de message qui s’applique à tous les journaux en plus de ce qui est fourni avec un appel de méthode `ILogger`.)</span><span class="sxs-lookup"><span data-stu-id="f9d82-904">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="f9d82-905">En cas de déploiement sur une application App Service, celle-ci applique les paramètres définis dans la section [Journaux App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="f9d82-905">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="f9d82-906">Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-906">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="f9d82-907">**Journalisation des applications (système de fichiers)**</span><span class="sxs-lookup"><span data-stu-id="f9d82-907">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="f9d82-908">**Journalisation des applications (objet blob)**</span><span class="sxs-lookup"><span data-stu-id="f9d82-908">**Application Logging (Blob)**</span></span>

<span data-ttu-id="f9d82-909">L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-909">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="f9d82-910">La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2.</span><span class="sxs-lookup"><span data-stu-id="f9d82-910">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="f9d82-911">Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="f9d82-911">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="f9d82-912">Le fournisseur fonctionne uniquement quand le projet s’exécute dans l’environnement Azure.</span><span class="sxs-lookup"><span data-stu-id="f9d82-912">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="f9d82-913">Il n’a aucun effet si le projet s’exécute localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.</span><span class="sxs-lookup"><span data-stu-id="f9d82-913">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="f9d82-914">Streaming des journaux Azure</span><span class="sxs-lookup"><span data-stu-id="f9d82-914">Azure log streaming</span></span>

<span data-ttu-id="f9d82-915">La diffusion en continu des journaux Azure permet d’afficher l’activité de journalisation en temps réel aux emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="f9d82-915">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="f9d82-916">Serveur d'applications</span><span class="sxs-lookup"><span data-stu-id="f9d82-916">The app server</span></span>
* <span data-ttu-id="f9d82-917">Serveur web</span><span class="sxs-lookup"><span data-stu-id="f9d82-917">The web server</span></span>
* <span data-ttu-id="f9d82-918">Suivi des demandes ayant échoué</span><span class="sxs-lookup"><span data-stu-id="f9d82-918">Failed request tracing</span></span>

<span data-ttu-id="f9d82-919">Pour configurer le streaming des journaux Azure :</span><span class="sxs-lookup"><span data-stu-id="f9d82-919">To configure Azure log streaming:</span></span>

* <span data-ttu-id="f9d82-920">Accédez à la page **Journaux App Service** dans le portail de votre application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-920">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="f9d82-921">Définissez **Journal des applications (Système de fichiers)** sur **Activé**.</span><span class="sxs-lookup"><span data-stu-id="f9d82-921">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="f9d82-922">Choisissez le **niveau** du journal.</span><span class="sxs-lookup"><span data-stu-id="f9d82-922">Choose the log **Level**.</span></span> <span data-ttu-id="f9d82-923">Ce paramètre ne s’applique qu’au streaming en rondins Azure, et non aux autres fournisseurs de connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-923">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="f9d82-924">Accédez à la page **Streaming des journaux** pour voir les messages d’application.</span><span class="sxs-lookup"><span data-stu-id="f9d82-924">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="f9d82-925">Ils sont consignés par application par le biais de l’interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="f9d82-925">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="f9d82-926">Journalisation des traces Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f9d82-926">Azure Application Insights trace logging</span></span>

<span data-ttu-id="f9d82-927">Le package de fournisseur [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit des journaux dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-927">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="f9d82-928">Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie.</span><span class="sxs-lookup"><span data-stu-id="f9d82-928">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="f9d82-929">Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-929">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="f9d82-930">Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9d82-930">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="f9d82-931">Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-931">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="f9d82-932">N’utilisez pas le [package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;destiné à ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="f9d82-932">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="f9d82-933">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="f9d82-933">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f9d82-934">Vue d'ensemble d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="f9d82-934">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="f9d82-935">[Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.</span><span class="sxs-lookup"><span data-stu-id="f9d82-935">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="f9d82-936">[Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="f9d82-936">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="f9d82-937">[Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="f9d82-937">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="f9d82-938">[Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="f9d82-938">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="f9d82-939">Fournisseurs de journalisation tiers</span><span class="sxs-lookup"><span data-stu-id="f9d82-939">Third-party logging providers</span></span>

<span data-ttu-id="f9d82-940">Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f9d82-940">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="f9d82-941">[elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-941">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="f9d82-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="f9d82-943">[JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="f9d82-943">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="f9d82-944">[KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="f9d82-944">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="f9d82-945">[Log4Net](https://logging.apache.org/log4net/) ([repo GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="f9d82-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="f9d82-946">[Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-946">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="f9d82-947">[NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="f9d82-947">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="f9d82-948">[Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f9d82-948">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="f9d82-949">[Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="f9d82-949">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="f9d82-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="f9d82-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="f9d82-951">Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f9d82-951">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f9d82-952">L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :</span><span class="sxs-lookup"><span data-stu-id="f9d82-952">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="f9d82-953">Ajoutez un package NuGet à votre projet.</span><span class="sxs-lookup"><span data-stu-id="f9d82-953">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="f9d82-954">Appelez `ILoggerFactory` une méthode d’extension fournie par le cadre d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="f9d82-954">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="f9d82-955">Pour plus d’informations, consultez la documentation de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="f9d82-955">For more information, see each provider's documentation.</span></span> <span data-ttu-id="f9d82-956">Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f9d82-956">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9d82-957">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f9d82-957">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
