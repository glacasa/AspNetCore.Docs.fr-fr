---
title: Journalisation dans .NET Core et ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le framework de journalisation fourni par le package NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: ca62e374c6031ca3c2d438df87f2d13636d9c612
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776101"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="ea2f0-103">Journalisation dans .NET Core et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea2f0-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ea2f0-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ea2f0-105">.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="ea2f0-106">Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="ea2f0-107">La plupart des exemples de code présentés dans cet article proviennent d’applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="ea2f0-108">Les parties spécifiques à la journalisation de ces extraits de code s’appliquent à n’importe quelle application .NET Core qui utilise l' [hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="ea2f0-109">Pour obtenir un exemple d’utilisation de l’hôte générique dans une application de console non Web, consultez le fichier *Program.cs* de l' [exemple d’application tâches en arrière-plan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="ea2f0-110">Le code de journalisation pour les applications sans hôte générique diffère dans la façon dont les [fournisseurs sont ajoutés](#add-providers) et les [enregistreurs d'événements créés](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="ea2f0-111">Des exemples de code non hôte sont présentés dans ces sections de l’article.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="ea2f0-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="ea2f0-113">Ajouter des fournisseurs</span><span class="sxs-lookup"><span data-stu-id="ea2f0-113">Add providers</span></span>

<span data-ttu-id="ea2f0-114">Un fournisseur de journalisation affiche ou stocke des journaux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="ea2f0-115">Par exemple, le fournisseur Console affiche les journaux dans la console, et le fournisseur Azure Application Insights les stocke dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="ea2f0-116">Il est possible d’envoyer les journaux à plusieurs endroits en ajoutant plusieurs fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="ea2f0-117">Pour ajouter un fournisseur dans une application qui utilise un hôte générique, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="ea2f0-118">Dans une application de console non hôte, appelez la méthode d’extension `Add{provider name}` du fournisseur lors de la création d’un `LoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="ea2f0-119">`LoggerFactory` et `AddConsole` requièrent une instruction `using` pour `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="ea2f0-120">Les modèles de projet ASP.NET Core par défaut appellent <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="ea2f0-121">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="ea2f0-122">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="ea2f0-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="ea2f0-124">[EventLog](#windows-eventlog-provider) (uniquement en cas d’exécution sur Windows)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="ea2f0-125">Vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="ea2f0-126">Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="ea2f0-127">Vous trouverez des informations sur les [fournisseurs de journalisation intégrés](#built-in-logging-providers) et les [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="ea2f0-128">Créer des journaux</span><span class="sxs-lookup"><span data-stu-id="ea2f0-128">Create logs</span></span>

<span data-ttu-id="ea2f0-129">Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="ea2f0-130">Dans une application web ou un service hébergé, obtenez un `ILogger` à partir de l’injection de dépendances (DI).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="ea2f0-131">Dans les applications de console non hôtes, utilisez le `LoggerFactory` pour créer un `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="ea2f0-132">L’exemple d’ASP.NET Core suivant crée un enregistreur d’événements de catégorie `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="ea2f0-133">La *catégorie* du journal est une chaîne associée à chaque journal.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="ea2f0-134">L’instance `ILogger<T>` fournie par l’injection de dépendances crée des journaux ayant comme catégorie un nom complet de type `T`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="ea2f0-135">L’exemple d’application de console non hôte suivant crée un enregistreur d’événements de catégorie `LoggingConsoleApp.Program`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="ea2f0-136">Dans les exemples d’ASP.NET Core et d’application de console suivants, l’enregistreur d’événements est utilisé pour créer des journaux de niveau `Information`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="ea2f0-137">Le *niveau* du journal indique la gravité de l’événement consigné.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="ea2f0-138">Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="ea2f0-139">Créer des journaux dans la classe Programme</span><span class="sxs-lookup"><span data-stu-id="ea2f0-139">Create logs in the Program class</span></span>

<span data-ttu-id="ea2f0-140">Pour écrire des journaux dans la classe `Program` d’une application ASP.NET Core, récupérez une instance `ILogger` de l’injection de dépendances après la création de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="ea2f0-141">La journalisation pendant la construction de l’hôte n’est pas prise en charge directement.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="ea2f0-142">Toutefois, un enregistreur d’événements distinct peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-142">However, a separate logger can be used.</span></span> <span data-ttu-id="ea2f0-143">Dans l’exemple suivant, un enregistreur d’événements [Serilog](https://serilog.net/) est utilisé pour `CreateHostBuilder`se connecter.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="ea2f0-144">`AddSerilog`utilise la configuration statique spécifiée dans `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="ea2f0-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="ea2f0-145">Créer des journaux dans la classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-145">Create logs in the Startup class</span></span>

<span data-ttu-id="ea2f0-146">Pour écrire des journaux dans la méthode `Startup.Configure` d’une application ASP.NET Core, incluez un paramètre `ILogger` dans la signature de la méthode :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="ea2f0-147">L’écriture de journaux avant la fin de l’installation du conteneur d’injection de dépendances dans la méthode `Startup.ConfigureServices` n’est pas prise en charge :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="ea2f0-148">L’injection d’un enregistreur d’événements dans le constructeur `Startup` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="ea2f0-149">L’injection d’un enregistreur d’événements dans la signature de méthode `Startup.ConfigureServices` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="ea2f0-150">La raison de cette restriction est que la journalisation dépend de l’injection de dépendances et de la configuration qui, à son tour, dépend de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="ea2f0-151">Le conteneur d’injection de dépendances n’est pas configuré avant que `ConfigureServices` soit terminé.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="ea2f0-152">L’injection de constructeur d’un enregistreur d’événements dans `Startup` fonctionne dans les versions antérieures d’ASP.NET Core, car un conteneur d’injection de dépendances distinct est créé pour l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="ea2f0-153">Pour plus d’informations sur la raison de la création d’un seul conteneur pour l’hôte générique, consultez l’[annonce de changement cassant](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="ea2f0-154">Si vous devez configurer un service qui dépend de `ILogger<T>`, vous pouvez toujours le faire à l’aide de l’injection de constructeur, ou avec une méthode de fabrique.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="ea2f0-155">L’approche de la méthode de fabrique est recommandée uniquement s’il n’y a aucune autre option.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="ea2f0-156">Supposons, par exemple, que vous deviez remplir une propriété avec un service à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="ea2f0-157">Le code en surbrillance précédent est une `Func` qui s’exécute la première fois que le conteneur d’injection de dépendances doit construire une instance de `MyService`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="ea2f0-158">Vous pouvez accéder à tous les services inscrits de cette manière.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="ea2f0-159">Créer des journaux dans éblouissant</span><span class="sxs-lookup"><span data-stu-id="ea2f0-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="ea2f0-160">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="ea2f0-160">Blazor WebAssembly</span></span>

<span data-ttu-id="ea2f0-161">Configurez la journalisation dans les applications webassembly éblouissantes avec la `WebAssemblyHostBuilder.Logging` propriété dans `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ea2f0-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="ea2f0-162">La `Logging` propriété est de type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, donc toutes les méthodes d’extension disponibles sur <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sont également disponibles sur `Logging`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="ea2f0-163">Se connecter aux composants Razor</span><span class="sxs-lookup"><span data-stu-id="ea2f0-163">Log in Razor components</span></span>

<span data-ttu-id="ea2f0-164">Les journaux respectent la configuration de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="ea2f0-165">La `using` directive pour <xref:Microsoft.Extensions.Logging> est requise pour prendre en charge les saisies semi-automatiques <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> IntelliSense <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>pour les API, telles que et.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="ea2f0-166">L’exemple suivant illustre la journalisation <xref:Microsoft.Extensions.Logging.ILogger> avec un dans les composants Razor :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="ea2f0-167">L’exemple suivant illustre la journalisation <xref:Microsoft.Extensions.Logging.ILoggerFactory> avec un dans les composants Razor :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="ea2f0-168">Aucune méthode d’enregistreur d’événements asynchrone</span><span class="sxs-lookup"><span data-stu-id="ea2f0-168">No asynchronous logger methods</span></span>

<span data-ttu-id="ea2f0-169">La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="ea2f0-170">Si votre magasin de données de journalisation est lent, n’écrivez pas directement dedans.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="ea2f0-171">Écrivez au départ les messages de journal dans un magasin rapide, puis déplacez-les dans le magasin lent.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="ea2f0-172">Par exemple, si vous vous connectez à SQL Server, évitez de le faire directement dans une méthode `Log`, car les méthodes `Log` sont synchrones.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="ea2f0-173">Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="ea2f0-174">Pour plus d’informations, consultez [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problème github.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="ea2f0-175">Configuration</span><span class="sxs-lookup"><span data-stu-id="ea2f0-175">Configuration</span></span>

<span data-ttu-id="ea2f0-176">La configuration de fournisseur de journalisation est fournie par un ou plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="ea2f0-177">Formats de fichiers (INI, JSON et XML).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="ea2f0-178">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="ea2f0-178">Command-line arguments.</span></span>
* <span data-ttu-id="ea2f0-179">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-179">Environment variables.</span></span>
* <span data-ttu-id="ea2f0-180">Objets .NET en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="ea2f0-181">Stockage [Secret Manager](xref:security/app-secrets) non chiffré.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="ea2f0-182">Magasin utilisateur chiffré comme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="ea2f0-183">Fournisseurs personnalisés (installés ou créés).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="ea2f0-184">Par exemple, la configuration de journalisation est généralement fournie par la section `Logging` des fichiers de paramètres d’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="ea2f0-185">L’exemple suivant montre le contenu d’un fichier *appsettings.Development.json* standard :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="ea2f0-186">La propriété `Logging` peut avoir un niveau `LogLevel` et des propriétés de module fournisseur d'informations (Console ici).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="ea2f0-187">La propriété `LogLevel` sous `Logging` spécifie le [niveau](#log-level) de journalisation minimal pour les catégories sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="ea2f0-188">Dans l’exemple, les catégories `System` et `Microsoft` sont consignées au niveau `Information`, et toutes les autres au niveau `Debug`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="ea2f0-189">Les autres propriétés sous `Logging` spécifient les fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="ea2f0-190">Cet exemple concerne le fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-190">The example is for the Console provider.</span></span> <span data-ttu-id="ea2f0-191">Si un fournisseur prend en charge les [étendues de journal](#log-scopes), `IncludeScopes` indique s’ils sont activés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="ea2f0-192">Une propriété de fournisseur (comme `Console` dans l’exemple) peut également spécifier une propriété `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="ea2f0-193">`LogLevel` sous un fournisseur spécifie les niveaux à consigner pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="ea2f0-194">Si les niveaux sont spécifiés dans `Logging.{providername}.LogLevel`, ils remplacent ce qui est défini dans `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="ea2f0-195">L’API de journalisation n’inclut pas de scénario pour modifier les niveaux de journal lorsqu’une application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="ea2f0-196">Toutefois, certains fournisseurs de configuration sont en charge du rechargement de la configuration, ce qui prend immédiatement effet sur la configuration de la journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="ea2f0-197">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider), qui est `CreateDefaultBuilder` ajouté par pour lire les fichiers de paramètres, recharge la configuration de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="ea2f0-198">Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="ea2f0-199">Pour plus d’informations sur l’implémentation des fournisseurs de configuration, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="ea2f0-200">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="ea2f0-200">Sample logging output</span></span>

<span data-ttu-id="ea2f0-201">Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console lorsque l’application est exécutée en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="ea2f0-202">Voici un exemple de sortie de la console :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-202">Here's an example of console output:</span></span>

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

<span data-ttu-id="ea2f0-203">Les journaux précédents ont été générés par une requête HTTP Get à l’exemple d’application à l’adresse `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="ea2f0-204">Voici un exemple des mêmes journaux tels qu’ils s’affichent dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="ea2f0-205">Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApiSample ».</span><span class="sxs-lookup"><span data-stu-id="ea2f0-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="ea2f0-206">Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="ea2f0-207">ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="ea2f0-208">Les autres sections de cet article détaillent certains points et présentent les options de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="ea2f0-209">Packages NuGet</span><span class="sxs-lookup"><span data-stu-id="ea2f0-209">NuGet packages</span></span>

<span data-ttu-id="ea2f0-210">Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="ea2f0-211">Catégorie de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-211">Log category</span></span>

<span data-ttu-id="ea2f0-212">Quand un objet `ILogger` est créé, une *catégorie* lui est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="ea2f0-213">Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="ea2f0-214">Si la catégorie peut être n’importe quelle chaîne, la convention est d’utiliser le nom de la classe, par exemple « TodoApi.Controllers.TodoController ».</span><span class="sxs-lookup"><span data-stu-id="ea2f0-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="ea2f0-215">Utilisez `ILogger<T>` pour obtenir une instance de `ILogger` qui utilise le nom de type complet `T` comme catégorie :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="ea2f0-216">Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="ea2f0-217">`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="ea2f0-218">Log level</span><span class="sxs-lookup"><span data-stu-id="ea2f0-218">Log level</span></span>

<span data-ttu-id="ea2f0-219">Chaque journal spécifie une valeur <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="ea2f0-220">Le niveau de journalisation indique la gravité ou l’importance.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="ea2f0-221">Vous pourriez par exemple écrire un journal `Information` lorsqu’une méthode se termine normalement et un journal `Warning` lorsqu’une méthode retourne un code de statut *404 Not Found*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="ea2f0-222">Le code suivant crée des journaux `Information` et `Warning` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="ea2f0-223">Dans le code précédent, le premier paramètre est [l’ID de l’événement de journal](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="ea2f0-224">Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="ea2f0-225">Les paramètres de méthode sont expliqués dans la section [Modèle de message](#log-message-template) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="ea2f0-226">Les méthodes de journal qui comportent le niveau dans leur nom (par exemple, `LogInformation` et `LogWarning`) sont des [méthodes d’extension pour ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="ea2f0-227">Elles appellent une méthode `Log` qui prend un paramètre `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="ea2f0-228">Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="ea2f0-229">Pour plus d’informations, voir <xref:Microsoft.Extensions.Logging.ILogger> et le [code source des extensions d’enregistreur d'événements](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="ea2f0-230">ASP.NET Core définit les niveaux de journalisation suivants, classés selon leur degré de gravité (du plus faible au plus élevé).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="ea2f0-231">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="ea2f0-231">Trace = 0</span></span>

  <span data-ttu-id="ea2f0-232">Informations en général exclusivement utiles à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="ea2f0-233">Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="ea2f0-234">*Désactivé par défaut.*</span><span class="sxs-lookup"><span data-stu-id="ea2f0-234">*Disabled by default.*</span></span>

* <span data-ttu-id="ea2f0-235">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="ea2f0-235">Debug = 1</span></span>

  <span data-ttu-id="ea2f0-236">Informations qui peuvent être utiles dans le développement et le débogage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="ea2f0-237">Exemple : `Entering method Configure with flag set to true.` En raison de leur volume élevé, n’activez les journaux de niveau `Debug` en production que pour résoudre des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="ea2f0-238">Information = 2</span><span class="sxs-lookup"><span data-stu-id="ea2f0-238">Information = 2</span></span>

  <span data-ttu-id="ea2f0-239">Informations de suivi du flux général de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="ea2f0-240">Ces journaux ont généralement une utilité à long terme.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="ea2f0-241">Exemple : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="ea2f0-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="ea2f0-242">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="ea2f0-242">Warning = 3</span></span>

  <span data-ttu-id="ea2f0-243">Informations sur les événements anormaux ou inattendus dans le flux de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="ea2f0-244">Il peut s’agir d’erreurs ou d’autres situations qui ne provoquent pas l’arrêt de l’application, mais qu’il peut être intéressant d’examiner.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="ea2f0-245">Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="ea2f0-246">Exemple : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="ea2f0-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="ea2f0-247">Error = 4</span><span class="sxs-lookup"><span data-stu-id="ea2f0-247">Error = 4</span></span>

  <span data-ttu-id="ea2f0-248">Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="ea2f0-249">Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="ea2f0-250">Exemple de message de journal : `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="ea2f0-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="ea2f0-251">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="ea2f0-251">Critical = 5</span></span>

  <span data-ttu-id="ea2f0-252">Fournit des informations sur des échecs qui nécessitent un examen immédiat.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-252">For failures that require immediate attention.</span></span> <span data-ttu-id="ea2f0-253">Exemples : perte de données, espace disque insuffisant.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="ea2f0-254">Le niveau de journalisation permet de contrôler le volume de la sortie de journal écrite sur un support de stockage ou dans une fenêtre d’affichage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="ea2f0-255">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-255">For example:</span></span>

* <span data-ttu-id="ea2f0-256">En production :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-256">In production:</span></span>
  * <span data-ttu-id="ea2f0-257">La `Trace` journalisation au `Information` niveau des niveaux de production génère un volume important de messages journaux détaillés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="ea2f0-258">Pour contrôler les coûts et ne pas dépasser les limites de `Trace` stockage `Information` des données, consignez les messages de niveau dans un magasin de données volumineux et à faible coût.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="ea2f0-259">La `Warning` journalisation `Critical` au niveau des niveaux produit généralement moins de messages journaux plus petits.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="ea2f0-260">Par conséquent, les coûts et les limites de stockage ne sont généralement pas un problème, ce qui se traduit par une plus grande flexibilité du choix des magasins de données.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="ea2f0-261">Lors du développement :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-261">During development:</span></span>
  * <span data-ttu-id="ea2f0-262">`Warning` Journalisation `Critical` des messages dans la console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="ea2f0-263">Ajoutez `Trace` des `Information` messages lors de la résolution des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="ea2f0-264">La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="ea2f0-265">ASP.NET Core écrit des journaux pour les événements de framework.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="ea2f0-266">Aucun journal du niveau `Debug` ou `Trace` n’était créé dans les exemples de journaux présentés plus haut dans cet article, puisque les journaux au-dessous du niveau `Information` étaient exclus.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="ea2f0-267">Voici un exemple de journaux Console produits par l’exemple d’application configurée pour afficher les journaux `Debug` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="ea2f0-268">ID d’événement de log</span><span class="sxs-lookup"><span data-stu-id="ea2f0-268">Log event ID</span></span>

<span data-ttu-id="ea2f0-269">Chaque journal peut spécifier un *ID d’événement*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="ea2f0-270">Pour cela, l’exemple d’application utilise une classe `LoggingEvents` définie localement :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="ea2f0-271">Un ID d’événement associe un jeu d’événements.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-271">An event ID associates a set of events.</span></span> <span data-ttu-id="ea2f0-272">Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="ea2f0-273">Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="ea2f0-274">Le fournisseur Debug n’affiche pas les ID d’événements.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="ea2f0-275">Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="ea2f0-276">Modèle de message de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-276">Log message template</span></span>

<span data-ttu-id="ea2f0-277">Chaque journal spécifie un modèle de message.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-277">Each log specifies a message template.</span></span> <span data-ttu-id="ea2f0-278">Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="ea2f0-279">Utilisez des noms et non des nombres pour les espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="ea2f0-280">L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="ea2f0-281">Dans le code suivant, on voit que les noms de paramètres sont hors séquence dans le modèle de message :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="ea2f0-282">Ce code crée un message de journal avec les valeurs des paramètres dans la séquence :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="ea2f0-283">Le framework de journalisation fonctionne ainsi pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="ea2f0-284">Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="ea2f0-285">C’est grâce à ces informations que les fournisseurs de journalisation peuvent stocker les valeurs des paramètres sous forme de champs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="ea2f0-286">Supposons par exemple que les appels de méthodes d’enregistreur d’événements se présentent ainsi :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="ea2f0-287">Si vous envoyez les journaux au Stockage Table Azure, chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journaux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="ea2f0-288">Une requête peut rechercher tous les journaux compris dans une plage `RequestTime` spécifique sans analyser le délai d’expiration du message texte.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="ea2f0-289">Journalisation des exceptions</span><span class="sxs-lookup"><span data-stu-id="ea2f0-289">Logging exceptions</span></span>

<span data-ttu-id="ea2f0-290">Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="ea2f0-291">Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="ea2f0-292">Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="ea2f0-293">Filtrage de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-293">Log filtering</span></span>

<span data-ttu-id="ea2f0-294">Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="ea2f0-295">Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="ea2f0-296">Pour supprimer tous les journaux, choisissez `LogLevel.None` comme niveau de journalisation minimal.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="ea2f0-297">La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="ea2f0-298">Créer des règles de filtre dans la configuration</span><span class="sxs-lookup"><span data-stu-id="ea2f0-298">Create filter rules in configuration</span></span>

<span data-ttu-id="ea2f0-299">Le code du modèle de `CreateDefaultBuilder` projet appelle pour configurer la journalisation des fournisseurs de console, de débogage et EventSource (ASP.net Core 2,2 ou version ultérieure).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="ea2f0-300">La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, conformément à ce qui a été expliqué [plus haut dans cet article](#configuration).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="ea2f0-301">Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="ea2f0-302">Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une pour tous les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="ea2f0-303">Une seule règle est choisie pour chaque fournisseur à la création d’un objet `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="ea2f0-304">Règles de filtre dans le code</span><span class="sxs-lookup"><span data-stu-id="ea2f0-304">Filter rules in code</span></span>

<span data-ttu-id="ea2f0-305">L'exemple suivant montre comment enregistrer des règles de filtre dans le code :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="ea2f0-306">Le second `AddFilter` spécifie le fournisseur Debug par son nom de type.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="ea2f0-307">Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="ea2f0-308">Mode d’application des règles de filtre</span><span class="sxs-lookup"><span data-stu-id="ea2f0-308">How filtering rules are applied</span></span>

<span data-ttu-id="ea2f0-309">Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="ea2f0-310">Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="ea2f0-311">Number</span><span class="sxs-lookup"><span data-stu-id="ea2f0-311">Number</span></span> | <span data-ttu-id="ea2f0-312">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="ea2f0-312">Provider</span></span>      | <span data-ttu-id="ea2f0-313">Catégories commençant par...</span><span class="sxs-lookup"><span data-stu-id="ea2f0-313">Categories that begin with ...</span></span>          | <span data-ttu-id="ea2f0-314">Niveau de journalisation minimum</span><span class="sxs-lookup"><span data-stu-id="ea2f0-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="ea2f0-315">1</span><span class="sxs-lookup"><span data-stu-id="ea2f0-315">1</span></span>      | <span data-ttu-id="ea2f0-316">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-316">Debug</span></span>         | <span data-ttu-id="ea2f0-317">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="ea2f0-317">All categories</span></span>                          | <span data-ttu-id="ea2f0-318">Information</span><span class="sxs-lookup"><span data-stu-id="ea2f0-318">Information</span></span>       |
| <span data-ttu-id="ea2f0-319">2</span><span class="sxs-lookup"><span data-stu-id="ea2f0-319">2</span></span>      | <span data-ttu-id="ea2f0-320">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-320">Console</span></span>       | <span data-ttu-id="ea2f0-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="ea2f0-322">Avertissement</span><span class="sxs-lookup"><span data-stu-id="ea2f0-322">Warning</span></span>           |
| <span data-ttu-id="ea2f0-323">3</span><span class="sxs-lookup"><span data-stu-id="ea2f0-323">3</span></span>      | <span data-ttu-id="ea2f0-324">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-324">Console</span></span>       | <span data-ttu-id="ea2f0-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="ea2f0-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="ea2f0-326">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-326">Debug</span></span>             |
| <span data-ttu-id="ea2f0-327">4</span><span class="sxs-lookup"><span data-stu-id="ea2f0-327">4</span></span>      | <span data-ttu-id="ea2f0-328">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-328">Console</span></span>       | <span data-ttu-id="ea2f0-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="ea2f0-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="ea2f0-330">Error</span><span class="sxs-lookup"><span data-stu-id="ea2f0-330">Error</span></span>             |
| <span data-ttu-id="ea2f0-331">5</span><span class="sxs-lookup"><span data-stu-id="ea2f0-331">5</span></span>      | <span data-ttu-id="ea2f0-332">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-332">Console</span></span>       | <span data-ttu-id="ea2f0-333">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="ea2f0-333">All categories</span></span>                          | <span data-ttu-id="ea2f0-334">Information</span><span class="sxs-lookup"><span data-stu-id="ea2f0-334">Information</span></span>       |
| <span data-ttu-id="ea2f0-335">6</span><span class="sxs-lookup"><span data-stu-id="ea2f0-335">6</span></span>      | <span data-ttu-id="ea2f0-336">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="ea2f0-336">All providers</span></span> | <span data-ttu-id="ea2f0-337">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="ea2f0-337">All categories</span></span>                          | <span data-ttu-id="ea2f0-338">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-338">Debug</span></span>             |
| <span data-ttu-id="ea2f0-339">7</span><span class="sxs-lookup"><span data-stu-id="ea2f0-339">7</span></span>      | <span data-ttu-id="ea2f0-340">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="ea2f0-340">All providers</span></span> | <span data-ttu-id="ea2f0-341">Système</span><span class="sxs-lookup"><span data-stu-id="ea2f0-341">System</span></span>                                  | <span data-ttu-id="ea2f0-342">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-342">Debug</span></span>             |
| <span data-ttu-id="ea2f0-343">8</span><span class="sxs-lookup"><span data-stu-id="ea2f0-343">8</span></span>      | <span data-ttu-id="ea2f0-344">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-344">Debug</span></span>         | <span data-ttu-id="ea2f0-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="ea2f0-345">Microsoft</span></span>                               | <span data-ttu-id="ea2f0-346">Trace</span><span class="sxs-lookup"><span data-stu-id="ea2f0-346">Trace</span></span>             |

<span data-ttu-id="ea2f0-347">À la création d’un objet `ILogger`, l’objet `ILoggerFactory` sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="ea2f0-348">Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="ea2f0-349">La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="ea2f0-350">L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="ea2f0-351">Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="ea2f0-352">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="ea2f0-353">À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="ea2f0-354">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="ea2f0-355">Si plusieurs règles sont sélectionnées, prenez la **dernière**.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="ea2f0-356">Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="ea2f0-357">Avec la liste de règles précédente, supposons que vous créez un objet `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="ea2f0-358">Les règles 1, 6 et 8 s’appliquent au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="ea2f0-359">La règle 8 est sélectionnée, car c’est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="ea2f0-360">Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="ea2f0-361">La règle 3 est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="ea2f0-362">L’instance `ILogger` ainsi produite envoie des journaux de niveau `Trace` ou supérieur au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="ea2f0-363">Les journaux de niveau `Debug` et supérieurs sont envoyés au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="ea2f0-364">Alias de fournisseur</span><span class="sxs-lookup"><span data-stu-id="ea2f0-364">Provider aliases</span></span>

<span data-ttu-id="ea2f0-365">Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="ea2f0-366">Pour les fournisseurs intégrés, utilisez les alias suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="ea2f0-367">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-367">Console</span></span>
* <span data-ttu-id="ea2f0-368">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-368">Debug</span></span>
* <span data-ttu-id="ea2f0-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-369">EventSource</span></span>
* <span data-ttu-id="ea2f0-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="ea2f0-370">EventLog</span></span>
* <span data-ttu-id="ea2f0-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-371">TraceSource</span></span>
* <span data-ttu-id="ea2f0-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="ea2f0-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="ea2f0-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="ea2f0-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="ea2f0-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="ea2f0-375">Niveau minimum par défaut</span><span class="sxs-lookup"><span data-stu-id="ea2f0-375">Default minimum level</span></span>

<span data-ttu-id="ea2f0-376">Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="ea2f0-377">L’exemple suivant montre comment définir le niveau minimum :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="ea2f0-378">Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="ea2f0-379">Fonctions de filtrage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-379">Filter functions</span></span>

<span data-ttu-id="ea2f0-380">Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="ea2f0-381">Le code de la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="ea2f0-382">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="ea2f0-383">Niveaux et les catégories de système</span><span class="sxs-lookup"><span data-stu-id="ea2f0-383">System categories and levels</span></span>

<span data-ttu-id="ea2f0-384">Voici quelques catégories utilisées par ASP.NET Core et Entity Framework Core, avec des notes sur les journaux associés :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="ea2f0-385">Category</span><span class="sxs-lookup"><span data-stu-id="ea2f0-385">Category</span></span>                            | <span data-ttu-id="ea2f0-386">Remarques</span><span class="sxs-lookup"><span data-stu-id="ea2f0-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="ea2f0-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="ea2f0-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="ea2f0-388">Diagnostics ASP.NET Core généraux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="ea2f0-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="ea2f0-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="ea2f0-390">Liste des clés considérées, trouvées et utilisées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="ea2f0-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="ea2f0-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="ea2f0-392">Hôtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-392">Hosts allowed.</span></span> |
| <span data-ttu-id="ea2f0-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="ea2f0-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="ea2f0-394">Temps de traitement des requêtes HTTP et heure de démarrage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="ea2f0-395">Liste des assemblys de démarrage d’hébergement chargés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="ea2f0-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="ea2f0-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="ea2f0-397">Diagnostics MVC et Razor.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="ea2f0-398">Liaison de données, exécution de filtres, compilation de vues, sélection d’actions.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="ea2f0-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="ea2f0-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="ea2f0-400">Informations de correspondance des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-400">Route matching information.</span></span> |
| <span data-ttu-id="ea2f0-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="ea2f0-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="ea2f0-402">Démarrage et arrêt de la connexion, et réponses persistantes.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="ea2f0-403">Informations du certificat HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="ea2f0-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="ea2f0-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="ea2f0-405">Fichiers pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-405">Files served.</span></span> |
| <span data-ttu-id="ea2f0-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="ea2f0-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="ea2f0-407">Diagnostics Entity Framework Core généraux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="ea2f0-408">Activité et configuration de la base de données, détection des modifications, migrations.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="ea2f0-409">Étendues de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-409">Log scopes</span></span>

 <span data-ttu-id="ea2f0-410">Une *étendue* peut regrouper un ensemble d’opérations logiques.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="ea2f0-411">Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="ea2f0-412">Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="ea2f0-413">Une étendue est un type `IDisposable` qui est retourné par la méthode <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>. Elle s’applique tant qu’elle n’est pas supprimée.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="ea2f0-414">Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="ea2f0-415">Le code suivant active les étendues pour le fournisseur Console :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="ea2f0-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ea2f0-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="ea2f0-417">La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="ea2f0-418">Pour plus d'informations sur la configuration, reportez-vous à la section [Configuration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="ea2f0-419">Chaque message de journal fournit les informations incluses dans l’étendue :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="ea2f0-420">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="ea2f0-420">Built-in logging providers</span></span>

<span data-ttu-id="ea2f0-421">ASP.NET Core contient les fournisseurs suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="ea2f0-422">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="ea2f0-423">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="ea2f0-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="ea2f0-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="ea2f0-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="ea2f0-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="ea2f0-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="ea2f0-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="ea2f0-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="ea2f0-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="ea2f0-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="ea2f0-430">Pour plus d’informations sur stdout et sur la journalisation du débogage avec le module ASP.NET Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="ea2f0-431">Fournisseur Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-431">Console provider</span></span>

<span data-ttu-id="ea2f0-432">Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="ea2f0-433">Pour voir la sortie de la journalisation Console, ouvrez une invite de commandes dans le dossier du projet et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="ea2f0-434">Fournisseur Debug</span><span class="sxs-lookup"><span data-stu-id="ea2f0-434">Debug provider</span></span>

<span data-ttu-id="ea2f0-435">Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (appels de la méthode `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="ea2f0-436">Sur Linux, ce fournisseur écrit la sortie de log dans */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="ea2f0-437">Fournisseur de source d’événements</span><span class="sxs-lookup"><span data-stu-id="ea2f0-437">Event Source provider</span></span>

<span data-ttu-id="ea2f0-438">Le package du fournisseur [Microsoft. extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) écrit dans une source d’événement multiplateforme portant le nom `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="ea2f0-439">Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="ea2f0-440">Le fournisseur de la source d’événements est `CreateDefaultBuilder` ajouté automatiquement lorsque est appelé pour générer l’hôte.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="ea2f0-441">outils de trace dotnet</span><span class="sxs-lookup"><span data-stu-id="ea2f0-441">dotnet trace tooling</span></span>

<span data-ttu-id="ea2f0-442">L’outil [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) est un outil global de l’interface CLI multiplateforme qui permet la collecte des traces .net core d’un processus en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="ea2f0-443">L’outil collecte les <xref:Microsoft.Extensions.Logging.EventSource> données du fournisseur à <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>l’aide d’un.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="ea2f0-444">Installez les outils de trace dotnet avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="ea2f0-445">Utilisez les outils de trace dotnet pour collecter une trace à partir d’une application :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="ea2f0-446">Si l’application ne crée pas l’hôte `CreateDefaultBuilder`avec, ajoutez le fournisseur de la [source d’événements](#event-source-provider) à la configuration de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="ea2f0-447">Exécutez l’application avec la `dotnet run` commande.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="ea2f0-448">Déterminez l’identificateur de processus (PID) de l’application .NET Core :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="ea2f0-449">Sur Windows, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="ea2f0-450">Gestionnaire des tâches (Ctrl + Alt + Suppr)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="ea2f0-451">TaskList, commande</span><span class="sxs-lookup"><span data-stu-id="ea2f0-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="ea2f0-452">Commande PowerShell d’extraction de processus</span><span class="sxs-lookup"><span data-stu-id="ea2f0-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="ea2f0-453">Sur Linux, utilisez la [commande pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="ea2f0-454">Recherchez le PID pour le processus qui porte le même nom que l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="ea2f0-455">Exécutez la `dotnet trace` commande.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="ea2f0-456">Syntaxe de la commande générale :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="ea2f0-457">Quand vous utilisez une interface de commande PowerShell, `--providers` Placez la valeur entre guillemets simples (`'`) :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="ea2f0-458">Sur les plateformes non-Windows, `-f speedscope` ajoutez l’option permettant de modifier le format du fichier de `speedscope`trace de sortie en.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="ea2f0-459">Mot clé</span><span class="sxs-lookup"><span data-stu-id="ea2f0-459">Keyword</span></span> | <span data-ttu-id="ea2f0-460">Description</span><span class="sxs-lookup"><span data-stu-id="ea2f0-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="ea2f0-461">1</span><span class="sxs-lookup"><span data-stu-id="ea2f0-461">1</span></span>       | <span data-ttu-id="ea2f0-462">Consignez les événements `LoggingEventSource`méta sur le.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="ea2f0-463">N’enregistre pas les `ILogger`événements à partir de).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="ea2f0-464">2</span><span class="sxs-lookup"><span data-stu-id="ea2f0-464">2</span></span>       | <span data-ttu-id="ea2f0-465">Active l' `Message` événement lorsque `ILogger.Log()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="ea2f0-466">Fournit des informations d’une façon de programmation (non mise en forme).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="ea2f0-467">4</span><span class="sxs-lookup"><span data-stu-id="ea2f0-467">4</span></span>       | <span data-ttu-id="ea2f0-468">Active l' `FormatMessage` événement lorsque `ILogger.Log()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="ea2f0-469">Fournit la version de chaîne mise en forme des informations.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="ea2f0-470">8</span><span class="sxs-lookup"><span data-stu-id="ea2f0-470">8</span></span>       | <span data-ttu-id="ea2f0-471">Active l' `MessageJson` événement lorsque `ILogger.Log()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="ea2f0-472">Fournit une représentation JSON des arguments.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="ea2f0-473">Niveau d'événement</span><span class="sxs-lookup"><span data-stu-id="ea2f0-473">Event Level</span></span> | <span data-ttu-id="ea2f0-474">Description</span><span class="sxs-lookup"><span data-stu-id="ea2f0-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="ea2f0-475">0</span><span class="sxs-lookup"><span data-stu-id="ea2f0-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="ea2f0-476">1</span><span class="sxs-lookup"><span data-stu-id="ea2f0-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="ea2f0-477">2</span><span class="sxs-lookup"><span data-stu-id="ea2f0-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="ea2f0-478">3</span><span class="sxs-lookup"><span data-stu-id="ea2f0-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="ea2f0-479">4</span><span class="sxs-lookup"><span data-stu-id="ea2f0-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="ea2f0-480">5</span><span class="sxs-lookup"><span data-stu-id="ea2f0-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="ea2f0-481">`FilterSpecs`les entrées `{Logger Category}` pour `{Event Level}` et représentent des conditions de filtrage de journal supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="ea2f0-482">Séparez `FilterSpecs` les entrées par un`;`point-virgule ().</span><span class="sxs-lookup"><span data-stu-id="ea2f0-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="ea2f0-483">Exemple utilisant un interpréteur de commandes Windows (**sans** guillemets `--providers` simples autour de la valeur) :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="ea2f0-484">La commande précédente active :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-484">The preceding command activates:</span></span>

   * <span data-ttu-id="ea2f0-485">Enregistreur d’événements de la source d’événements pour`4`produire des chaînes mises`2`en forme () pour les erreurs ().</span><span class="sxs-lookup"><span data-stu-id="ea2f0-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="ea2f0-486">`Microsoft.AspNetCore.Hosting`journalisation au `Informational` niveau de la`4`journalisation ().</span><span class="sxs-lookup"><span data-stu-id="ea2f0-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="ea2f0-487">Arrêtez les outils de trace dotnet en appuyant sur la touche entrée ou Ctrl + C.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="ea2f0-488">La trace est enregistrée avec le nom *trace. NetTrace* dans le dossier où la `dotnet trace` commande est exécutée.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="ea2f0-489">Ouvrez la trace avec [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="ea2f0-490">Ouvrez le fichier *trace. NetTrace* et explorez les événements de trace.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="ea2f0-491">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-491">For more information, see:</span></span>

* <span data-ttu-id="ea2f0-492">[Utilitaire trace for Performance Analysis (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentation .net Core)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="ea2f0-493">[Utilitaire trace for Performance Analysis (dotnet-trace) (documentation sur le](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) référentiel GitHub dotnet/Diagnostics)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="ea2f0-494">[LoggingEventSource, classe](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (Explorateur d’API .net)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="ea2f0-495">[Source de référence LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; pour obtenir la source de référence pour une version différente, remplacez `release/{Version}`la branche `{Version}` par, où est la version de ASP.net Core souhaitée.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="ea2f0-496">[Perfview](#perfview) &ndash; utile pour l’affichage des traces de la source d’événements.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="ea2f0-497">Perfview</span><span class="sxs-lookup"><span data-stu-id="ea2f0-497">Perfview</span></span>

<span data-ttu-id="ea2f0-498">Utilisez l' [utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="ea2f0-499">Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="ea2f0-500">Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="ea2f0-501">(N’oubliez pas d’inclure l’astérisque au début de la chaîne.)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="ea2f0-503">Fournisseur EventLog Windows</span><span class="sxs-lookup"><span data-stu-id="ea2f0-503">Windows EventLog provider</span></span>

<span data-ttu-id="ea2f0-504">Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="ea2f0-505">Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) vous permettent de passer à <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="ea2f0-506">Si `null` ou n’est pas spécifié, les paramètres par défaut suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="ea2f0-507">`LogName`&ndash; « Application »</span><span class="sxs-lookup"><span data-stu-id="ea2f0-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="ea2f0-508">`SourceName`&ndash; « Runtime .net »</span><span class="sxs-lookup"><span data-stu-id="ea2f0-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="ea2f0-509">`MachineName` &ndash; ordinateur local</span><span class="sxs-lookup"><span data-stu-id="ea2f0-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="ea2f0-510">Les événements sont consignés pour le [niveau d’avertissement et supérieur](#log-level).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="ea2f0-511">Pour consigner les `Warning`événements inférieurs à, définissez explicitement le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="ea2f0-512">Par exemple, ajoutez le code suivant au fichier *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="ea2f0-513">Fournisseur TraceSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-513">TraceSource provider</span></span>

<span data-ttu-id="ea2f0-514">Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="ea2f0-515">Les [surcharges AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) vous permettent de passer un commutateur de source et un écouteur de suivi.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="ea2f0-516">Pour pouvoir utiliser ce fournisseur, il faut que l’application s’exécute sur .NET Framework (au lieu de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="ea2f0-517">Le fournisseur peut acheminer les messages vers différents [détecteurs d’événements](/dotnet/framework/debug-trace-profile/trace-listeners), comme <xref:System.Diagnostics.TextWriterTraceListener> (utilisé dans l’exemple d’application).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="ea2f0-518">Fournisseur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea2f0-518">Azure App Service provider</span></span>

<span data-ttu-id="ea2f0-519">Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="ea2f0-520">Le fournisseur de package n’est pas inclus dans le framework partagé.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="ea2f0-521">Pour utiliser le fournisseur, ajoutez le package du fournisseur au projet.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="ea2f0-522">Pour configurer les paramètres du fournisseur, utilisez <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> et <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="ea2f0-523">En cas de déploiement sur une application App Service, celle-ci applique les paramètres définis dans la section [Journaux App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="ea2f0-524">Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="ea2f0-525">**Journalisation des applications (système de fichiers)**</span><span class="sxs-lookup"><span data-stu-id="ea2f0-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="ea2f0-526">**Journalisation des applications (objet blob)**</span><span class="sxs-lookup"><span data-stu-id="ea2f0-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="ea2f0-527">L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="ea2f0-528">La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="ea2f0-529">Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="ea2f0-530">Le fournisseur fonctionne uniquement quand le projet s’exécute dans l’environnement Azure.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="ea2f0-531">Il n’a aucun effet si le projet s’exécute localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="ea2f0-532">Streaming des journaux Azure</span><span class="sxs-lookup"><span data-stu-id="ea2f0-532">Azure log streaming</span></span>

<span data-ttu-id="ea2f0-533">La diffusion en continu des journaux Azure permet d’afficher l’activité de journalisation en temps réel aux emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="ea2f0-534">Serveur d'applications</span><span class="sxs-lookup"><span data-stu-id="ea2f0-534">The app server</span></span>
* <span data-ttu-id="ea2f0-535">Serveur web</span><span class="sxs-lookup"><span data-stu-id="ea2f0-535">The web server</span></span>
* <span data-ttu-id="ea2f0-536">Suivi des demandes ayant échoué</span><span class="sxs-lookup"><span data-stu-id="ea2f0-536">Failed request tracing</span></span>

<span data-ttu-id="ea2f0-537">Pour configurer le streaming des journaux Azure :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="ea2f0-538">Accédez à la page **Journaux App Service** dans le portail de votre application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="ea2f0-539">Définissez **Journal des applications (Système de fichiers)** sur **Activé**.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="ea2f0-540">Choisissez le **niveau** du journal.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-540">Choose the log **Level**.</span></span> <span data-ttu-id="ea2f0-541">Ce paramètre s’applique uniquement à la diffusion en continu de journaux Azure, pas aux autres fournisseurs de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="ea2f0-542">Accédez à la page **Streaming des journaux** pour voir les messages d’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="ea2f0-543">Ils sont consignés par application par le biais de l’interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="ea2f0-544">Journalisation des traces Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="ea2f0-545">Le package de fournisseur [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit des journaux dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="ea2f0-546">Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="ea2f0-547">Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="ea2f0-548">Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="ea2f0-549">Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="ea2f0-550">N’utilisez pas le [package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;destiné à ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="ea2f0-551">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="ea2f0-552">Vue d'ensemble d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="ea2f0-553">[Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="ea2f0-554">[Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="ea2f0-555">[Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="ea2f0-556">[Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="ea2f0-557">Fournisseurs de journalisation tiers</span><span class="sxs-lookup"><span data-stu-id="ea2f0-557">Third-party logging providers</span></span>

<span data-ttu-id="ea2f0-558">Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="ea2f0-559">[elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="ea2f0-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="ea2f0-561">[JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="ea2f0-562">[KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="ea2f0-563">[Log4net](https://logging.apache.org/log4net/) ([GitHub référentiel](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="ea2f0-564">[Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="ea2f0-565">[NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="ea2f0-566">[Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="ea2f0-567">[Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="ea2f0-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="ea2f0-569">Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="ea2f0-570">L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="ea2f0-571">Ajoutez un package NuGet à votre projet.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="ea2f0-572">Appeler une `ILoggerFactory` méthode d’extension fournie par le Framework de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="ea2f0-573">Pour plus d’informations, consultez la documentation de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="ea2f0-574">Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea2f0-575">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ea2f0-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ea2f0-576">Par [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ea2f0-577">.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="ea2f0-578">Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="ea2f0-579">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="ea2f0-580">Ajouter des fournisseurs</span><span class="sxs-lookup"><span data-stu-id="ea2f0-580">Add providers</span></span>

<span data-ttu-id="ea2f0-581">Un fournisseur de journalisation affiche ou stocke des journaux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="ea2f0-582">Par exemple, le fournisseur Console affiche les journaux dans la console, et le fournisseur Azure Application Insights les stocke dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="ea2f0-583">Il est possible d’envoyer les journaux à plusieurs endroits en ajoutant plusieurs fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="ea2f0-584">Pour ajouter un fournisseur, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="ea2f0-585">Le code précédent nécessite des références à `Microsoft.Extensions.Logging` et `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="ea2f0-586">Le modèle de projet par défaut appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="ea2f0-587">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-587">Console</span></span>
* <span data-ttu-id="ea2f0-588">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-588">Debug</span></span>
* <span data-ttu-id="ea2f0-589">EventSource (à partir d’ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="ea2f0-590">Si vous utilisez `CreateDefaultBuilder`, vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="ea2f0-591">Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="ea2f0-592">Vous trouverez des informations sur les [fournisseurs de journalisation intégrés](#built-in-logging-providers) et les [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="ea2f0-593">Créer des journaux</span><span class="sxs-lookup"><span data-stu-id="ea2f0-593">Create logs</span></span>

<span data-ttu-id="ea2f0-594">Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="ea2f0-595">Dans une application web ou un service hébergé, obtenez un `ILogger` à partir de l’injection de dépendances (DI).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="ea2f0-596">Dans les applications de console non hôtes, utilisez le `LoggerFactory` pour créer un `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="ea2f0-597">L’exemple d’ASP.NET Core suivant crée un enregistreur d’événements de catégorie `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="ea2f0-598">La *catégorie* du journal est une chaîne associée à chaque journal.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="ea2f0-599">L’instance `ILogger<T>` fournie par l’injection de dépendances crée des journaux ayant comme catégorie un nom complet de type `T`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="ea2f0-600">Dans les exemples d’ASP.NET Core et d’application de console suivants, l’enregistreur d’événements est utilisé pour créer des journaux de niveau `Information`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="ea2f0-601">Le *niveau* du journal indique la gravité de l’événement consigné.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="ea2f0-602">Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="ea2f0-603">Créer des journaux au démarrage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-603">Create logs in Startup</span></span>

<span data-ttu-id="ea2f0-604">Pour écrire des journaux dans la classe `Startup`, ajoutez un paramètre `ILogger` dans la signature de constructeur :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="ea2f0-605">Créer des journaux dans la classe Programme</span><span class="sxs-lookup"><span data-stu-id="ea2f0-605">Create logs in the Program class</span></span>

<span data-ttu-id="ea2f0-606">Pour écrire des journaux dans la classe `Program`, récupérez une instance `ILogger` auprès de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="ea2f0-607">La journalisation pendant la construction de l’hôte n’est pas prise en charge directement.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="ea2f0-608">Toutefois, un enregistreur d’événements distinct peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-608">However, a separate logger can be used.</span></span> <span data-ttu-id="ea2f0-609">Dans l’exemple suivant, un enregistreur d’événements [Serilog](https://serilog.net/) est utilisé pour `CreateWebHostBuilder`se connecter.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="ea2f0-610">`AddSerilog`utilise la configuration statique spécifiée dans `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="ea2f0-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="ea2f0-611">Aucune méthode d’enregistreur d’événements asynchrone</span><span class="sxs-lookup"><span data-stu-id="ea2f0-611">No asynchronous logger methods</span></span>

<span data-ttu-id="ea2f0-612">La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="ea2f0-613">Si votre magasin de données de journalisation est lent, n’écrivez pas directement dedans.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="ea2f0-614">Écrivez au départ les messages de journal dans un magasin rapide, puis déplacez-les dans le magasin lent.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="ea2f0-615">Par exemple, si vous vous connectez à SQL Server, évitez de le faire directement dans une méthode `Log`, car les méthodes `Log` sont synchrones.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="ea2f0-616">Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="ea2f0-617">Pour plus d’informations, consultez [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problème github.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="ea2f0-618">Configuration</span><span class="sxs-lookup"><span data-stu-id="ea2f0-618">Configuration</span></span>

<span data-ttu-id="ea2f0-619">La configuration de fournisseur de journalisation est fournie par un ou plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="ea2f0-620">Formats de fichiers (INI, JSON et XML).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="ea2f0-621">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="ea2f0-621">Command-line arguments.</span></span>
* <span data-ttu-id="ea2f0-622">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-622">Environment variables.</span></span>
* <span data-ttu-id="ea2f0-623">Objets .NET en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="ea2f0-624">Stockage [Secret Manager](xref:security/app-secrets) non chiffré.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="ea2f0-625">Magasin utilisateur chiffré comme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="ea2f0-626">Fournisseurs personnalisés (installés ou créés).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="ea2f0-627">Par exemple, la configuration de journalisation est généralement fournie par la section `Logging` des fichiers de paramètres d’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="ea2f0-628">L’exemple suivant montre le contenu d’un fichier *appsettings.Development.json* standard :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="ea2f0-629">La propriété `Logging` peut avoir un niveau `LogLevel` et des propriétés de module fournisseur d'informations (Console ici).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="ea2f0-630">La propriété `LogLevel` sous `Logging` spécifie le [niveau](#log-level) de journalisation minimal pour les catégories sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="ea2f0-631">Dans l’exemple, les catégories `System` et `Microsoft` sont consignées au niveau `Information`, et toutes les autres au niveau `Debug`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="ea2f0-632">Les autres propriétés sous `Logging` spécifient les fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="ea2f0-633">Cet exemple concerne le fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-633">The example is for the Console provider.</span></span> <span data-ttu-id="ea2f0-634">Si un fournisseur prend en charge les [étendues de journal](#log-scopes), `IncludeScopes` indique s’ils sont activés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="ea2f0-635">Une propriété de fournisseur (comme `Console` dans l’exemple) peut également spécifier une propriété `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="ea2f0-636">`LogLevel` sous un fournisseur spécifie les niveaux à consigner pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="ea2f0-637">Si les niveaux sont spécifiés dans `Logging.{providername}.LogLevel`, ils remplacent ce qui est défini dans `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="ea2f0-638">L’API de journalisation n’inclut pas de scénario pour modifier les niveaux de journal lorsqu’une application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="ea2f0-639">Toutefois, certains fournisseurs de configuration sont en charge du rechargement de la configuration, ce qui prend immédiatement effet sur la configuration de la journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="ea2f0-640">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider), qui est `CreateDefaultBuilder` ajouté par pour lire les fichiers de paramètres, recharge la configuration de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="ea2f0-641">Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="ea2f0-642">Pour plus d’informations sur l’implémentation des fournisseurs de configuration, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="ea2f0-643">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="ea2f0-643">Sample logging output</span></span>

<span data-ttu-id="ea2f0-644">Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console lorsque l’application est exécutée en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="ea2f0-645">Voici un exemple de sortie de la console :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-645">Here's an example of console output:</span></span>

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

<span data-ttu-id="ea2f0-646">Les journaux précédents ont été générés par une requête HTTP Get à l’exemple d’application à l’adresse `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="ea2f0-647">Voici un exemple des mêmes journaux tels qu’ils s’affichent dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="ea2f0-648">Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApi ».</span><span class="sxs-lookup"><span data-stu-id="ea2f0-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="ea2f0-649">Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="ea2f0-650">ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="ea2f0-651">Les autres sections de cet article détaillent certains points et présentent les options de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="ea2f0-652">Packages NuGet</span><span class="sxs-lookup"><span data-stu-id="ea2f0-652">NuGet packages</span></span>

<span data-ttu-id="ea2f0-653">Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="ea2f0-654">Catégorie de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-654">Log category</span></span>

<span data-ttu-id="ea2f0-655">Quand un objet `ILogger` est créé, une *catégorie* lui est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="ea2f0-656">Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="ea2f0-657">Si la catégorie peut être n’importe quelle chaîne, la convention est d’utiliser le nom de la classe, par exemple « TodoApi.Controllers.TodoController ».</span><span class="sxs-lookup"><span data-stu-id="ea2f0-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="ea2f0-658">Utilisez `ILogger<T>` pour obtenir une instance de `ILogger` qui utilise le nom de type complet `T` comme catégorie :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="ea2f0-659">Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="ea2f0-660">`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="ea2f0-661">Log level</span><span class="sxs-lookup"><span data-stu-id="ea2f0-661">Log level</span></span>

<span data-ttu-id="ea2f0-662">Chaque journal spécifie une valeur <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="ea2f0-663">Le niveau de journalisation indique la gravité ou l’importance.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="ea2f0-664">Vous pourriez par exemple écrire un journal `Information` lorsqu’une méthode se termine normalement et un journal `Warning` lorsqu’une méthode retourne un code de statut *404 Not Found*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="ea2f0-665">Le code suivant crée des journaux `Information` et `Warning` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="ea2f0-666">Dans le code précédent, le premier paramètre est [l’ID de l’événement de journal](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="ea2f0-667">Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="ea2f0-668">Les paramètres de méthode sont expliqués dans la section [Modèle de message](#log-message-template) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="ea2f0-669">Les méthodes de journal qui comportent le niveau dans leur nom (par exemple, `LogInformation` et `LogWarning`) sont des [méthodes d’extension pour ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="ea2f0-670">Elles appellent une méthode `Log` qui prend un paramètre `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="ea2f0-671">Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="ea2f0-672">Pour plus d’informations, voir <xref:Microsoft.Extensions.Logging.ILogger> et le [code source des extensions d’enregistreur d'événements](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="ea2f0-673">ASP.NET Core définit les niveaux de journalisation suivants, classés selon leur degré de gravité (du plus faible au plus élevé).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="ea2f0-674">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="ea2f0-674">Trace = 0</span></span>

  <span data-ttu-id="ea2f0-675">Informations en général exclusivement utiles à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="ea2f0-676">Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="ea2f0-677">*Désactivé par défaut.*</span><span class="sxs-lookup"><span data-stu-id="ea2f0-677">*Disabled by default.*</span></span>

* <span data-ttu-id="ea2f0-678">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="ea2f0-678">Debug = 1</span></span>

  <span data-ttu-id="ea2f0-679">Informations qui peuvent être utiles dans le développement et le débogage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="ea2f0-680">Exemple : `Entering method Configure with flag set to true.` En raison de leur volume élevé, n’activez les journaux de niveau `Debug` en production que pour résoudre des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="ea2f0-681">Information = 2</span><span class="sxs-lookup"><span data-stu-id="ea2f0-681">Information = 2</span></span>

  <span data-ttu-id="ea2f0-682">Informations de suivi du flux général de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="ea2f0-683">Ces journaux ont généralement une utilité à long terme.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="ea2f0-684">Exemple : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="ea2f0-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="ea2f0-685">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="ea2f0-685">Warning = 3</span></span>

  <span data-ttu-id="ea2f0-686">Informations sur les événements anormaux ou inattendus dans le flux de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="ea2f0-687">Il peut s’agir d’erreurs ou d’autres situations qui ne provoquent pas l’arrêt de l’application, mais qu’il peut être intéressant d’examiner.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="ea2f0-688">Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="ea2f0-689">Exemple : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="ea2f0-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="ea2f0-690">Error = 4</span><span class="sxs-lookup"><span data-stu-id="ea2f0-690">Error = 4</span></span>

  <span data-ttu-id="ea2f0-691">Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="ea2f0-692">Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="ea2f0-693">Exemple de message de journal : `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="ea2f0-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="ea2f0-694">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="ea2f0-694">Critical = 5</span></span>

  <span data-ttu-id="ea2f0-695">Fournit des informations sur des échecs qui nécessitent un examen immédiat.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-695">For failures that require immediate attention.</span></span> <span data-ttu-id="ea2f0-696">Exemples : perte de données, espace disque insuffisant.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="ea2f0-697">Le niveau de journalisation permet de contrôler le volume de la sortie de journal écrite sur un support de stockage ou dans une fenêtre d’affichage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="ea2f0-698">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-698">For example:</span></span>

* <span data-ttu-id="ea2f0-699">En production :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-699">In production:</span></span>
  * <span data-ttu-id="ea2f0-700">La `Trace` journalisation au `Information` niveau des niveaux de production génère un volume important de messages journaux détaillés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="ea2f0-701">Pour contrôler les coûts et ne pas dépasser les limites de `Trace` stockage `Information` des données, consignez les messages de niveau dans un magasin de données volumineux et à faible coût.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="ea2f0-702">La `Warning` journalisation `Critical` au niveau des niveaux produit généralement moins de messages journaux plus petits.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="ea2f0-703">Par conséquent, les coûts et les limites de stockage ne sont généralement pas un problème, ce qui se traduit par une plus grande flexibilité du choix des magasins de données.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="ea2f0-704">Lors du développement :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-704">During development:</span></span>
  * <span data-ttu-id="ea2f0-705">`Warning` Journalisation `Critical` des messages dans la console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="ea2f0-706">Ajoutez `Trace` des `Information` messages lors de la résolution des problèmes.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="ea2f0-707">La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="ea2f0-708">ASP.NET Core écrit des journaux pour les événements de framework.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="ea2f0-709">Aucun journal du niveau `Debug` ou `Trace` n’était créé dans les exemples de journaux présentés plus haut dans cet article, puisque les journaux au-dessous du niveau `Information` étaient exclus.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="ea2f0-710">Voici un exemple de journaux Console produits par l’exemple d’application configurée pour afficher les journaux `Debug` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="ea2f0-711">ID d’événement de log</span><span class="sxs-lookup"><span data-stu-id="ea2f0-711">Log event ID</span></span>

<span data-ttu-id="ea2f0-712">Chaque journal peut spécifier un *ID d’événement*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="ea2f0-713">Pour cela, l’exemple d’application utilise une classe `LoggingEvents` définie localement :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="ea2f0-714">Un ID d’événement associe un jeu d’événements.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-714">An event ID associates a set of events.</span></span> <span data-ttu-id="ea2f0-715">Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="ea2f0-716">Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="ea2f0-717">Le fournisseur Debug n’affiche pas les ID d’événements.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="ea2f0-718">Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="ea2f0-719">Modèle de message de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-719">Log message template</span></span>

<span data-ttu-id="ea2f0-720">Chaque journal spécifie un modèle de message.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-720">Each log specifies a message template.</span></span> <span data-ttu-id="ea2f0-721">Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="ea2f0-722">Utilisez des noms et non des nombres pour les espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="ea2f0-723">L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="ea2f0-724">Dans le code suivant, on voit que les noms de paramètres sont hors séquence dans le modèle de message :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="ea2f0-725">Ce code crée un message de journal avec les valeurs des paramètres dans la séquence :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="ea2f0-726">Le framework de journalisation fonctionne ainsi pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="ea2f0-727">Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="ea2f0-728">C’est grâce à ces informations que les fournisseurs de journalisation peuvent stocker les valeurs des paramètres sous forme de champs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="ea2f0-729">Supposons par exemple que les appels de méthodes d’enregistreur d’événements se présentent ainsi :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="ea2f0-730">Si vous envoyez les journaux au Stockage Table Azure, chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journaux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="ea2f0-731">Une requête peut rechercher tous les journaux compris dans une plage `RequestTime` spécifique sans analyser le délai d’expiration du message texte.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="ea2f0-732">Journalisation des exceptions</span><span class="sxs-lookup"><span data-stu-id="ea2f0-732">Logging exceptions</span></span>

<span data-ttu-id="ea2f0-733">Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="ea2f0-734">Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="ea2f0-735">Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="ea2f0-736">Filtrage de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-736">Log filtering</span></span>

<span data-ttu-id="ea2f0-737">Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="ea2f0-738">Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="ea2f0-739">Pour supprimer tous les journaux, choisissez `LogLevel.None` comme niveau de journalisation minimal.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="ea2f0-740">La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="ea2f0-741">Créer des règles de filtre dans la configuration</span><span class="sxs-lookup"><span data-stu-id="ea2f0-741">Create filter rules in configuration</span></span>

<span data-ttu-id="ea2f0-742">Le code du modèle de `CreateDefaultBuilder` projet appelle pour configurer la journalisation des fournisseurs de console, de débogage et EventSource (ASP.net Core 2,2 ou version ultérieure).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="ea2f0-743">La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, conformément à ce qui a été expliqué [plus haut dans cet article](#configuration).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="ea2f0-744">Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="ea2f0-745">Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une pour tous les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="ea2f0-746">Une seule règle est choisie pour chaque fournisseur à la création d’un objet `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="ea2f0-747">Règles de filtre dans le code</span><span class="sxs-lookup"><span data-stu-id="ea2f0-747">Filter rules in code</span></span>

<span data-ttu-id="ea2f0-748">L'exemple suivant montre comment enregistrer des règles de filtre dans le code :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="ea2f0-749">Le second `AddFilter` spécifie le fournisseur Debug par son nom de type.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="ea2f0-750">Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="ea2f0-751">Mode d’application des règles de filtre</span><span class="sxs-lookup"><span data-stu-id="ea2f0-751">How filtering rules are applied</span></span>

<span data-ttu-id="ea2f0-752">Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="ea2f0-753">Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="ea2f0-754">Number</span><span class="sxs-lookup"><span data-stu-id="ea2f0-754">Number</span></span> | <span data-ttu-id="ea2f0-755">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="ea2f0-755">Provider</span></span>      | <span data-ttu-id="ea2f0-756">Catégories commençant par...</span><span class="sxs-lookup"><span data-stu-id="ea2f0-756">Categories that begin with ...</span></span>          | <span data-ttu-id="ea2f0-757">Niveau de journalisation minimum</span><span class="sxs-lookup"><span data-stu-id="ea2f0-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="ea2f0-758">1</span><span class="sxs-lookup"><span data-stu-id="ea2f0-758">1</span></span>      | <span data-ttu-id="ea2f0-759">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-759">Debug</span></span>         | <span data-ttu-id="ea2f0-760">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="ea2f0-760">All categories</span></span>                          | <span data-ttu-id="ea2f0-761">Information</span><span class="sxs-lookup"><span data-stu-id="ea2f0-761">Information</span></span>       |
| <span data-ttu-id="ea2f0-762">2</span><span class="sxs-lookup"><span data-stu-id="ea2f0-762">2</span></span>      | <span data-ttu-id="ea2f0-763">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-763">Console</span></span>       | <span data-ttu-id="ea2f0-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="ea2f0-765">Avertissement</span><span class="sxs-lookup"><span data-stu-id="ea2f0-765">Warning</span></span>           |
| <span data-ttu-id="ea2f0-766">3</span><span class="sxs-lookup"><span data-stu-id="ea2f0-766">3</span></span>      | <span data-ttu-id="ea2f0-767">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-767">Console</span></span>       | <span data-ttu-id="ea2f0-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="ea2f0-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="ea2f0-769">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-769">Debug</span></span>             |
| <span data-ttu-id="ea2f0-770">4</span><span class="sxs-lookup"><span data-stu-id="ea2f0-770">4</span></span>      | <span data-ttu-id="ea2f0-771">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-771">Console</span></span>       | <span data-ttu-id="ea2f0-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="ea2f0-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="ea2f0-773">Error</span><span class="sxs-lookup"><span data-stu-id="ea2f0-773">Error</span></span>             |
| <span data-ttu-id="ea2f0-774">5</span><span class="sxs-lookup"><span data-stu-id="ea2f0-774">5</span></span>      | <span data-ttu-id="ea2f0-775">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-775">Console</span></span>       | <span data-ttu-id="ea2f0-776">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="ea2f0-776">All categories</span></span>                          | <span data-ttu-id="ea2f0-777">Information</span><span class="sxs-lookup"><span data-stu-id="ea2f0-777">Information</span></span>       |
| <span data-ttu-id="ea2f0-778">6</span><span class="sxs-lookup"><span data-stu-id="ea2f0-778">6</span></span>      | <span data-ttu-id="ea2f0-779">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="ea2f0-779">All providers</span></span> | <span data-ttu-id="ea2f0-780">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="ea2f0-780">All categories</span></span>                          | <span data-ttu-id="ea2f0-781">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-781">Debug</span></span>             |
| <span data-ttu-id="ea2f0-782">7</span><span class="sxs-lookup"><span data-stu-id="ea2f0-782">7</span></span>      | <span data-ttu-id="ea2f0-783">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="ea2f0-783">All providers</span></span> | <span data-ttu-id="ea2f0-784">Système</span><span class="sxs-lookup"><span data-stu-id="ea2f0-784">System</span></span>                                  | <span data-ttu-id="ea2f0-785">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-785">Debug</span></span>             |
| <span data-ttu-id="ea2f0-786">8</span><span class="sxs-lookup"><span data-stu-id="ea2f0-786">8</span></span>      | <span data-ttu-id="ea2f0-787">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-787">Debug</span></span>         | <span data-ttu-id="ea2f0-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="ea2f0-788">Microsoft</span></span>                               | <span data-ttu-id="ea2f0-789">Trace</span><span class="sxs-lookup"><span data-stu-id="ea2f0-789">Trace</span></span>             |

<span data-ttu-id="ea2f0-790">À la création d’un objet `ILogger`, l’objet `ILoggerFactory` sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="ea2f0-791">Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="ea2f0-792">La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="ea2f0-793">L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="ea2f0-794">Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="ea2f0-795">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="ea2f0-796">À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="ea2f0-797">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="ea2f0-798">Si plusieurs règles sont sélectionnées, prenez la **dernière**.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="ea2f0-799">Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="ea2f0-800">Avec la liste de règles précédente, supposons que vous créez un objet `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="ea2f0-801">Les règles 1, 6 et 8 s’appliquent au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="ea2f0-802">La règle 8 est sélectionnée, car c’est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="ea2f0-803">Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="ea2f0-804">La règle 3 est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="ea2f0-805">L’instance `ILogger` ainsi produite envoie des journaux de niveau `Trace` ou supérieur au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="ea2f0-806">Les journaux de niveau `Debug` et supérieurs sont envoyés au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="ea2f0-807">Alias de fournisseur</span><span class="sxs-lookup"><span data-stu-id="ea2f0-807">Provider aliases</span></span>

<span data-ttu-id="ea2f0-808">Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="ea2f0-809">Pour les fournisseurs intégrés, utilisez les alias suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="ea2f0-810">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-810">Console</span></span>
* <span data-ttu-id="ea2f0-811">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-811">Debug</span></span>
* <span data-ttu-id="ea2f0-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-812">EventSource</span></span>
* <span data-ttu-id="ea2f0-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="ea2f0-813">EventLog</span></span>
* <span data-ttu-id="ea2f0-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-814">TraceSource</span></span>
* <span data-ttu-id="ea2f0-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="ea2f0-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="ea2f0-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="ea2f0-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="ea2f0-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="ea2f0-818">Niveau minimum par défaut</span><span class="sxs-lookup"><span data-stu-id="ea2f0-818">Default minimum level</span></span>

<span data-ttu-id="ea2f0-819">Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="ea2f0-820">L’exemple suivant montre comment définir le niveau minimum :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="ea2f0-821">Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="ea2f0-822">Fonctions de filtrage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-822">Filter functions</span></span>

<span data-ttu-id="ea2f0-823">Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="ea2f0-824">Le code de la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="ea2f0-825">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="ea2f0-826">Niveaux et les catégories de système</span><span class="sxs-lookup"><span data-stu-id="ea2f0-826">System categories and levels</span></span>

<span data-ttu-id="ea2f0-827">Voici quelques catégories utilisées par ASP.NET Core et Entity Framework Core, avec des notes sur les journaux associés :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="ea2f0-828">Category</span><span class="sxs-lookup"><span data-stu-id="ea2f0-828">Category</span></span>                            | <span data-ttu-id="ea2f0-829">Remarques</span><span class="sxs-lookup"><span data-stu-id="ea2f0-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="ea2f0-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="ea2f0-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="ea2f0-831">Diagnostics ASP.NET Core généraux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="ea2f0-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="ea2f0-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="ea2f0-833">Liste des clés considérées, trouvées et utilisées.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="ea2f0-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="ea2f0-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="ea2f0-835">Hôtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-835">Hosts allowed.</span></span> |
| <span data-ttu-id="ea2f0-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="ea2f0-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="ea2f0-837">Temps de traitement des requêtes HTTP et heure de démarrage.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="ea2f0-838">Liste des assemblys de démarrage d’hébergement chargés.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="ea2f0-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="ea2f0-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="ea2f0-840">MVC et Razor Diagnostics.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="ea2f0-841">Liaison de données, exécution de filtres, compilation de vues, sélection d’actions.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="ea2f0-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="ea2f0-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="ea2f0-843">Informations de correspondance des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-843">Route matching information.</span></span> |
| <span data-ttu-id="ea2f0-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="ea2f0-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="ea2f0-845">Démarrage et arrêt de la connexion, et réponses persistantes.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="ea2f0-846">Informations du certificat HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="ea2f0-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="ea2f0-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="ea2f0-848">Fichiers pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-848">Files served.</span></span> |
| <span data-ttu-id="ea2f0-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="ea2f0-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="ea2f0-850">Diagnostics Entity Framework Core généraux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="ea2f0-851">Activité et configuration de la base de données, détection des modifications, migrations.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="ea2f0-852">Étendues de journal</span><span class="sxs-lookup"><span data-stu-id="ea2f0-852">Log scopes</span></span>

 <span data-ttu-id="ea2f0-853">Une *étendue* peut regrouper un ensemble d’opérations logiques.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="ea2f0-854">Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="ea2f0-855">Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="ea2f0-856">Une étendue est un type `IDisposable` qui est retourné par la méthode <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>. Elle s’applique tant qu’elle n’est pas supprimée.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="ea2f0-857">Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="ea2f0-858">Le code suivant active les étendues pour le fournisseur Console :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="ea2f0-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ea2f0-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="ea2f0-860">La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="ea2f0-861">Pour plus d'informations sur la configuration, reportez-vous à la section [Configuration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="ea2f0-862">Chaque message de journal fournit les informations incluses dans l’étendue :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="ea2f0-863">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="ea2f0-863">Built-in logging providers</span></span>

<span data-ttu-id="ea2f0-864">ASP.NET Core contient les fournisseurs suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="ea2f0-865">Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="ea2f0-866">Débogage</span><span class="sxs-lookup"><span data-stu-id="ea2f0-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="ea2f0-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="ea2f0-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="ea2f0-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="ea2f0-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="ea2f0-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="ea2f0-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="ea2f0-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="ea2f0-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="ea2f0-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="ea2f0-873">Pour plus d’informations sur stdout et sur la journalisation du débogage avec le module ASP.NET Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="ea2f0-874">Fournisseur Console</span><span class="sxs-lookup"><span data-stu-id="ea2f0-874">Console provider</span></span>

<span data-ttu-id="ea2f0-875">Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="ea2f0-876">Pour voir la sortie de la journalisation Console, ouvrez une invite de commandes dans le dossier du projet et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="ea2f0-877">Fournisseur Debug</span><span class="sxs-lookup"><span data-stu-id="ea2f0-877">Debug provider</span></span>

<span data-ttu-id="ea2f0-878">Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (appels de la méthode `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="ea2f0-879">Sur Linux, ce fournisseur écrit la sortie de log dans */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="ea2f0-880">Fournisseur de source d’événements</span><span class="sxs-lookup"><span data-stu-id="ea2f0-880">Event Source provider</span></span>

<span data-ttu-id="ea2f0-881">Le package du fournisseur [Microsoft. extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) écrit dans une source d’événement multiplateforme portant le nom `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="ea2f0-882">Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="ea2f0-883">Le fournisseur de la source d’événements est `CreateDefaultBuilder` ajouté automatiquement lorsque est appelé pour générer l’hôte.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="ea2f0-884">Utilisez l' [utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="ea2f0-885">Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="ea2f0-886">Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="ea2f0-887">(N’oubliez pas d’inclure l’astérisque au début de la chaîne.)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="ea2f0-889">Fournisseur EventLog Windows</span><span class="sxs-lookup"><span data-stu-id="ea2f0-889">Windows EventLog provider</span></span>

<span data-ttu-id="ea2f0-890">Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="ea2f0-891">Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) vous permettent de passer à <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="ea2f0-892">Si `null` ou n’est pas spécifié, les paramètres par défaut suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="ea2f0-893">`LogName`&ndash; « Application »</span><span class="sxs-lookup"><span data-stu-id="ea2f0-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="ea2f0-894">`SourceName`&ndash; « Runtime .net »</span><span class="sxs-lookup"><span data-stu-id="ea2f0-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="ea2f0-895">`MachineName` &ndash; ordinateur local</span><span class="sxs-lookup"><span data-stu-id="ea2f0-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="ea2f0-896">Les événements sont consignés pour le [niveau d’avertissement et supérieur](#log-level).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="ea2f0-897">Pour consigner les `Warning`événements inférieurs à, définissez explicitement le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="ea2f0-898">Par exemple, ajoutez le code suivant au fichier *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="ea2f0-899">Fournisseur TraceSource</span><span class="sxs-lookup"><span data-stu-id="ea2f0-899">TraceSource provider</span></span>

<span data-ttu-id="ea2f0-900">Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="ea2f0-901">Les [surcharges AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) vous permettent de passer un commutateur de source et un écouteur de suivi.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="ea2f0-902">Pour pouvoir utiliser ce fournisseur, il faut que l’application s’exécute sur .NET Framework (au lieu de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="ea2f0-903">Le fournisseur peut acheminer les messages vers différents [détecteurs d’événements](/dotnet/framework/debug-trace-profile/trace-listeners), comme <xref:System.Diagnostics.TextWriterTraceListener> (utilisé dans l’exemple d’application).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="ea2f0-904">Fournisseur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea2f0-904">Azure App Service provider</span></span>

<span data-ttu-id="ea2f0-905">Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="ea2f0-906">Le package du fournisseur n’est pas inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="ea2f0-907">Lorsque vous ciblez .NET framework ou référencez le métapackage `Microsoft.AspNetCore.App`, ajoutez le package de fournisseur dans le projet.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="ea2f0-908">Une surcharge <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permet de transmettre <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="ea2f0-909">L’objet de paramètres peut remplacer les paramètres par défaut, comme le modèle de sortie de journalisation, le nom d’objet blob et la limite de taille de fichier.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="ea2f0-910">(*Modèle de sortie* est un modèle de message qui s’applique à tous les journaux en plus de ce qui est fourni avec un appel de méthode `ILogger`.)</span><span class="sxs-lookup"><span data-stu-id="ea2f0-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="ea2f0-911">En cas de déploiement sur une application App Service, celle-ci applique les paramètres définis dans la section [Journaux App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="ea2f0-912">Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="ea2f0-913">**Journalisation des applications (système de fichiers)**</span><span class="sxs-lookup"><span data-stu-id="ea2f0-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="ea2f0-914">**Journalisation des applications (objet blob)**</span><span class="sxs-lookup"><span data-stu-id="ea2f0-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="ea2f0-915">L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="ea2f0-916">La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="ea2f0-917">Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="ea2f0-918">Le fournisseur fonctionne uniquement quand le projet s’exécute dans l’environnement Azure.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="ea2f0-919">Il n’a aucun effet si le projet s’exécute localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="ea2f0-920">Streaming des journaux Azure</span><span class="sxs-lookup"><span data-stu-id="ea2f0-920">Azure log streaming</span></span>

<span data-ttu-id="ea2f0-921">La diffusion en continu des journaux Azure permet d’afficher l’activité de journalisation en temps réel aux emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="ea2f0-922">Serveur d'applications</span><span class="sxs-lookup"><span data-stu-id="ea2f0-922">The app server</span></span>
* <span data-ttu-id="ea2f0-923">Serveur web</span><span class="sxs-lookup"><span data-stu-id="ea2f0-923">The web server</span></span>
* <span data-ttu-id="ea2f0-924">Suivi des demandes ayant échoué</span><span class="sxs-lookup"><span data-stu-id="ea2f0-924">Failed request tracing</span></span>

<span data-ttu-id="ea2f0-925">Pour configurer le streaming des journaux Azure :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="ea2f0-926">Accédez à la page **Journaux App Service** dans le portail de votre application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="ea2f0-927">Définissez **Journal des applications (Système de fichiers)** sur **Activé**.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="ea2f0-928">Choisissez le **niveau** du journal.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-928">Choose the log **Level**.</span></span> <span data-ttu-id="ea2f0-929">Ce paramètre s’applique uniquement à la diffusion en continu de journaux Azure, pas aux autres fournisseurs de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="ea2f0-930">Accédez à la page **Streaming des journaux** pour voir les messages d’application.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="ea2f0-931">Ils sont consignés par application par le biais de l’interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="ea2f0-932">Journalisation des traces Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="ea2f0-933">Le package de fournisseur [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit des journaux dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="ea2f0-934">Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="ea2f0-935">Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="ea2f0-936">Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="ea2f0-937">Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="ea2f0-938">N’utilisez pas le [package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;destiné à ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="ea2f0-939">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="ea2f0-940">Vue d'ensemble d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="ea2f0-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="ea2f0-941">[Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="ea2f0-942">[Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="ea2f0-943">[Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="ea2f0-944">[Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="ea2f0-945">Fournisseurs de journalisation tiers</span><span class="sxs-lookup"><span data-stu-id="ea2f0-945">Third-party logging providers</span></span>

<span data-ttu-id="ea2f0-946">Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="ea2f0-947">[elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="ea2f0-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="ea2f0-949">[JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="ea2f0-950">[KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="ea2f0-951">[Log4net](https://logging.apache.org/log4net/) ([GitHub référentiel](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="ea2f0-952">[Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="ea2f0-953">[NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="ea2f0-954">[Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="ea2f0-955">[Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="ea2f0-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="ea2f0-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="ea2f0-957">Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="ea2f0-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="ea2f0-958">L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :</span><span class="sxs-lookup"><span data-stu-id="ea2f0-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="ea2f0-959">Ajoutez un package NuGet à votre projet.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="ea2f0-960">Appeler une `ILoggerFactory` méthode d’extension fournie par le Framework de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="ea2f0-961">Pour plus d’informations, consultez la documentation de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="ea2f0-962">Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ea2f0-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea2f0-963">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ea2f0-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
