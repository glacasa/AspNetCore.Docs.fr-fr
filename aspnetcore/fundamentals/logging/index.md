---
title: Journalisation dans .NET Core et ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le framework de journalisation fourni par le package NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330733"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="565ab-103">Journalisation dans .NET Core et ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="565ab-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="565ab-104">Par [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="565ab-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="565ab-105">.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés.</span><span class="sxs-lookup"><span data-stu-id="565ab-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="565ab-106">Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.</span><span class="sxs-lookup"><span data-stu-id="565ab-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="565ab-107">La plupart des exemples de code présentés dans cet article proviennent d’applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="565ab-108">Les parties spécifiques à la journalisation de ces extraits de code s’appliquent à n’importe quelle application .NET Core qui utilise l' [hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="565ab-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="565ab-109">Les modèles d’application Web ASP.NET Core utilisent l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="565ab-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="565ab-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="565ab-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="565ab-111">Fournisseurs de journalisation</span><span class="sxs-lookup"><span data-stu-id="565ab-111">Logging providers</span></span>

<span data-ttu-id="565ab-112">Les fournisseurs de journalisation stockent les journaux, à l’exception du `Console` fournisseur qui affiche les journaux.</span><span class="sxs-lookup"><span data-stu-id="565ab-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="565ab-113">Par exemple, le fournisseur Azure Application Insights stocke les journaux dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="565ab-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="565ab-114">Plusieurs fournisseurs peuvent être activés.</span><span class="sxs-lookup"><span data-stu-id="565ab-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="565ab-115">Les modèles d’application Web ASP.NET Core par défaut :</span><span class="sxs-lookup"><span data-stu-id="565ab-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="565ab-116">Utilisez l' [hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="565ab-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="565ab-117">Appelez <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , qui ajoute les fournisseurs de journalisation suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="565ab-118">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-118">Console</span></span>](#console)
  * [<span data-ttu-id="565ab-119">Déboguer</span><span class="sxs-lookup"><span data-stu-id="565ab-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="565ab-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="565ab-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="565ab-121">[EventLog](#welog): Windows uniquement</span><span class="sxs-lookup"><span data-stu-id="565ab-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="565ab-122">Le code précédent montre la `Program` classe créée avec les modèles d’application web ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="565ab-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="565ab-123">Les sections suivantes fournissent des exemples basés sur les modèles d’application Web ASP.NET Core, qui utilisent l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="565ab-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="565ab-124">Les [applications de console non-hôte](#nhca) sont présentées plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="565ab-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="565ab-125">Pour remplacer l’ensemble par défaut des fournisseurs de journalisation ajoutés par `Host.CreateDefaultBuilder` , appelez `ClearProviders` et ajoutez les fournisseurs de journalisation requis.</span><span class="sxs-lookup"><span data-stu-id="565ab-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="565ab-126">Par exemple, le code suivant :</span><span class="sxs-lookup"><span data-stu-id="565ab-126">For example, the following code:</span></span>

* <span data-ttu-id="565ab-127">Appelle <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> pour supprimer toutes les <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances du générateur.</span><span class="sxs-lookup"><span data-stu-id="565ab-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="565ab-128">Ajoute le fournisseur de journalisation de la [console](#console) .</span><span class="sxs-lookup"><span data-stu-id="565ab-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="565ab-129">Pour obtenir des fournisseurs supplémentaires, consultez :</span><span class="sxs-lookup"><span data-stu-id="565ab-129">For additional providers, see:</span></span>

* [<span data-ttu-id="565ab-130">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="565ab-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="565ab-131">[Fournisseurs de journalisation tiers](#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="565ab-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="565ab-132">Créer des journaux</span><span class="sxs-lookup"><span data-stu-id="565ab-132">Create logs</span></span>

<span data-ttu-id="565ab-133">Pour créer des journaux, utilisez un <xref:Microsoft.Extensions.Logging.ILogger%601> objet à partir de l' [injection de dépendances](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="565ab-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="565ab-134">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="565ab-134">The following example:</span></span>

* <span data-ttu-id="565ab-135">Crée un enregistreur d’événements, `ILogger<AboutModel>` , qui utilise une *catégorie* de journal du nom qualifié complet du type `AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="565ab-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="565ab-136">La catégorie du journal est une chaîne associée à chaque journal.</span><span class="sxs-lookup"><span data-stu-id="565ab-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="565ab-137">Appels <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> à consigner au `Information` niveau.</span><span class="sxs-lookup"><span data-stu-id="565ab-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="565ab-138">Le *niveau* du journal indique la gravité de l’événement consigné.</span><span class="sxs-lookup"><span data-stu-id="565ab-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="565ab-139">Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="565ab-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="565ab-140">Pour plus d’informations sur Blazor , consultez [créer des journaux dans Blazor et Blazor WebAssembly ](#clib) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="565ab-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="565ab-141">[Créer des journaux dans main et Startup](#clms) montre comment créer des journaux dans `Main` et `Startup` .</span><span class="sxs-lookup"><span data-stu-id="565ab-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="565ab-142">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="565ab-142">Configure logging</span></span>

<span data-ttu-id="565ab-143">La configuration de la journalisation est généralement fournie par la `Logging` section de *appSettings*. `{Environment}` fichiers *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="565ab-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="565ab-144">Le *appsettings.Development.jssuivant sur* le fichier est généré par les modèles d’application Web ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="565ab-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="565ab-145">Dans le code JSON précédent :</span><span class="sxs-lookup"><span data-stu-id="565ab-145">In the preceding JSON:</span></span>

* <span data-ttu-id="565ab-146">Les `"Default"` `"Microsoft"` catégories, et `"Microsoft.Hosting.Lifetime"` sont spécifiées.</span><span class="sxs-lookup"><span data-stu-id="565ab-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="565ab-147">La `"Microsoft"` catégorie s’applique à toutes les catégories qui commencent par `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="565ab-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="565ab-148">Par exemple, ce paramètre s’applique à la `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` catégorie.</span><span class="sxs-lookup"><span data-stu-id="565ab-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="565ab-149">La `"Microsoft"` catégorie enregistre les journaux au niveau du journal et au niveau `Warning` supérieur.</span><span class="sxs-lookup"><span data-stu-id="565ab-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="565ab-150">La catégorie `"Microsoft.Hosting.Lifetime"` est plus spécifique que la catégorie `"Microsoft"` , de sorte que la `"Microsoft.Hosting.Lifetime"` catégorie enregistre les journaux au niveau du journal « information » et supérieur.</span><span class="sxs-lookup"><span data-stu-id="565ab-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="565ab-151">Un module fournisseur d’informations spécifique n’étant pas spécifié, `LogLevel` s’applique à tous les fournisseurs de journalisation activés, à l’exception du journal des [événements Windows](#welog).</span><span class="sxs-lookup"><span data-stu-id="565ab-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="565ab-152">La `Logging` propriété peut avoir <xref:Microsoft.Extensions.Logging.LogLevel> et enregistrer les propriétés du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="565ab-153">`LogLevel`Spécifie le [niveau](#log-level) minimal à enregistrer pour les catégories sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="565ab-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="565ab-154">Dans le code JSON précédent, les `Information` `Warning` niveaux de journalisation sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="565ab-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="565ab-155">`LogLevel`indique la gravité du journal et les plages de 0 à 6 :</span><span class="sxs-lookup"><span data-stu-id="565ab-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="565ab-156">`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 et `None` = 6.</span><span class="sxs-lookup"><span data-stu-id="565ab-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="565ab-157">Lorsqu’un `LogLevel` est spécifié, la journalisation est activée pour les messages au niveau spécifié et supérieur.</span><span class="sxs-lookup"><span data-stu-id="565ab-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="565ab-158">Dans le code JSON précédent, la `Default` catégorie est journalisée pour `Information` et les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="565ab-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="565ab-159">Par exemple, les messages,, `Information` `Warning` `Error` et `Critical` sont journalisés.</span><span class="sxs-lookup"><span data-stu-id="565ab-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="565ab-160">Si aucun `LogLevel` n’est spécifié, la journalisation est définie par défaut sur le `Information` niveau.</span><span class="sxs-lookup"><span data-stu-id="565ab-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="565ab-161">Pour plus d’informations, consultez [niveaux de journalisation](#llvl).</span><span class="sxs-lookup"><span data-stu-id="565ab-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="565ab-162">Une propriété de fournisseur peut spécifier une `LogLevel` propriété.</span><span class="sxs-lookup"><span data-stu-id="565ab-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="565ab-163">`LogLevel`sous un fournisseur spécifie les niveaux de journalisation pour ce fournisseur, et remplace les paramètres du journal non fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="565ab-164">Prenez en compte les *appsettings.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="565ab-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="565ab-165">Paramètres dans les `Logging.{providername}.LogLevel` paramètres de remplacement dans `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="565ab-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="565ab-166">Dans le code JSON précédent, le `Debug` niveau de journalisation par défaut du fournisseur est défini sur `Information` :</span><span class="sxs-lookup"><span data-stu-id="565ab-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="565ab-167">Le paramètre précédent spécifie le `Information` niveau de journalisation pour chaque `Logging:Debug:` catégorie, à l’exception de `Microsoft.Hosting` .</span><span class="sxs-lookup"><span data-stu-id="565ab-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="565ab-168">Quand une catégorie spécifique est indiquée, la catégorie spécifique remplace la catégorie par défaut.</span><span class="sxs-lookup"><span data-stu-id="565ab-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="565ab-169">Dans le JSON précédent, les `Logging:Debug:LogLevel` catégories `"Microsoft.Hosting"` et `"Default"` remplacent les paramètres dans`Logging:LogLevel`</span><span class="sxs-lookup"><span data-stu-id="565ab-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="565ab-170">Le niveau de journal minimal peut être spécifié pour l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="565ab-171">Fournisseurs spécifiques : par exemple,`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="565ab-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="565ab-172">Catégories spécifiques : par exemple,`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="565ab-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="565ab-173">Tous les fournisseurs et toutes les catégories :`Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="565ab-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="565ab-174">Les journaux situés en dessous du niveau minimal ne sont ***pas***:</span><span class="sxs-lookup"><span data-stu-id="565ab-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="565ab-175">Passé au fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-175">Passed to the provider.</span></span>
* <span data-ttu-id="565ab-176">Consigné ou affiché.</span><span class="sxs-lookup"><span data-stu-id="565ab-176">Logged or displayed.</span></span>

<span data-ttu-id="565ab-177">Pour supprimer tous les journaux, spécifiez [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel).</span><span class="sxs-lookup"><span data-stu-id="565ab-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="565ab-178">`LogLevel.None`a une valeur de 6, qui est supérieure à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="565ab-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="565ab-179">Si un fournisseur prend en charge les [étendues de journal](#logscopes), `IncludeScopes` indique s’ils sont activés.</span><span class="sxs-lookup"><span data-stu-id="565ab-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="565ab-180">Pour plus d’informations, consultez [étendues de journaux](#logscopes)</span><span class="sxs-lookup"><span data-stu-id="565ab-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="565ab-181">Le *appsettings.jssuivant sur* le fichier contient tous les fournisseurs activés par défaut :</span><span class="sxs-lookup"><span data-stu-id="565ab-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="565ab-182">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="565ab-182">In the preceding sample:</span></span>

* <span data-ttu-id="565ab-183">Les catégories et les niveaux ne sont pas des valeurs suggérées.</span><span class="sxs-lookup"><span data-stu-id="565ab-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="565ab-184">L’exemple est fourni pour afficher tous les fournisseurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="565ab-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="565ab-185">Paramètres dans les `Logging.{providername}.LogLevel` paramètres de remplacement dans `Logging.LogLevel` .</span><span class="sxs-lookup"><span data-stu-id="565ab-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="565ab-186">Par exemple, le niveau dans `Debug.LogLevel.Default` remplace le niveau dans `LogLevel.Default` .</span><span class="sxs-lookup"><span data-stu-id="565ab-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="565ab-187">Chaque *alias* de fournisseur par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="565ab-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="565ab-188">Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="565ab-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="565ab-189">Les alias des fournisseurs intégrés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="565ab-190">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-190">Console</span></span>
  * <span data-ttu-id="565ab-191">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-191">Debug</span></span>
  * <span data-ttu-id="565ab-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="565ab-192">EventSource</span></span>
  * <span data-ttu-id="565ab-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="565ab-193">EventLog</span></span>
  * <span data-ttu-id="565ab-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="565ab-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="565ab-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="565ab-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="565ab-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="565ab-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="565ab-197">Définir le niveau de journalisation à l’aide d’une ligne de commande, de variables d’environnement et d’autres configurations</span><span class="sxs-lookup"><span data-stu-id="565ab-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="565ab-198">Le niveau de journal peut être défini par l’un des [fournisseurs de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="565ab-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="565ab-199">Les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="565ab-199">The following commands:</span></span>

* <span data-ttu-id="565ab-200">Affectez à la clé d’environnement la `Logging:LogLevel:Microsoft` valeur `Information` sur Windows.</span><span class="sxs-lookup"><span data-stu-id="565ab-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="565ab-201">Testez les paramètres lors de l’utilisation d’une application créée avec les modèles d’application Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="565ab-202">La `dotnet run` commande doit être exécutée dans le répertoire du projet après l’utilisation de `set` .</span><span class="sxs-lookup"><span data-stu-id="565ab-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="565ab-203">Le paramètre d’environnement précédent :</span><span class="sxs-lookup"><span data-stu-id="565ab-203">The preceding environment setting:</span></span>

* <span data-ttu-id="565ab-204">Est défini uniquement dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.</span><span class="sxs-lookup"><span data-stu-id="565ab-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="565ab-205">N’est pas lu par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="565ab-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="565ab-206">La commande [setx](/windows-server/administration/windows-commands/setx) suivante définit également la clé et la valeur de l’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="565ab-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="565ab-207">Contrairement à `set` , `setx` les paramètres sont conservés.</span><span class="sxs-lookup"><span data-stu-id="565ab-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="565ab-208">Le `/M` commutateur définit la variable dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="565ab-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="565ab-209">Si `/M` n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="565ab-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="565ab-210">Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** .</span><span class="sxs-lookup"><span data-stu-id="565ab-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="565ab-211">Azure App Service paramètres de l’application sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="565ab-212">Chiffré au repos et transmis sur un canal chiffré.</span><span class="sxs-lookup"><span data-stu-id="565ab-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="565ab-213">Exposés en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="565ab-213">Exposed as environment variables.</span></span>

<span data-ttu-id="565ab-214">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="565ab-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="565ab-215">Pour plus d’informations sur la définition de valeurs de configuration ASP.NET Core à l’aide de variables d’environnement, consultez [variables d’environnement](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="565ab-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="565ab-216">Pour plus d’informations sur l’utilisation d’autres sources de configuration, notamment la ligne de commande, Azure Key Vault, la configuration de Azure App, d’autres formats de fichier, etc., consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="565ab-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="565ab-217">Mode d’application des règles de filtre</span><span class="sxs-lookup"><span data-stu-id="565ab-217">How filtering rules are applied</span></span>

<span data-ttu-id="565ab-218">À la création d’un objet <xref:Microsoft.Extensions.Logging.ILogger%601>, l’objet <xref:Microsoft.Extensions.Logging.ILoggerFactory> sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="565ab-219">Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="565ab-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="565ab-220">La règle la plus spécifique pour chaque paire fournisseur et catégorie est sélectionnée parmi les règles disponibles.</span><span class="sxs-lookup"><span data-stu-id="565ab-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="565ab-221">L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :</span><span class="sxs-lookup"><span data-stu-id="565ab-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="565ab-222">Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias.</span><span class="sxs-lookup"><span data-stu-id="565ab-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="565ab-223">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.</span><span class="sxs-lookup"><span data-stu-id="565ab-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="565ab-224">À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant.</span><span class="sxs-lookup"><span data-stu-id="565ab-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="565ab-225">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.</span><span class="sxs-lookup"><span data-stu-id="565ab-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="565ab-226">Si plusieurs règles sont sélectionnées, prenez la **dernière**.</span><span class="sxs-lookup"><span data-stu-id="565ab-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="565ab-227">Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="565ab-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="565ab-228">Journalisation de la sortie de dotnet Run et de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="565ab-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="565ab-229">Les journaux créés avec les [fournisseurs de journalisation par défaut](#lp) sont affichés :</span><span class="sxs-lookup"><span data-stu-id="565ab-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="565ab-230">Dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="565ab-230">In Visual Studio</span></span>
  * <span data-ttu-id="565ab-231">Dans la fenêtre sortie de débogage lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="565ab-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="565ab-232">Dans la fenêtre serveur Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="565ab-233">Dans la fenêtre de console lorsque l’application est exécutée `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="565ab-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="565ab-234">Les journaux qui commencent par des catégories « Microsoft » proviennent du code ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="565ab-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="565ab-235">Les ASP.NET Core et le code d’application utilisent les mêmes API de journalisation et fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="565ab-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="565ab-236">Catégorie de journal</span><span class="sxs-lookup"><span data-stu-id="565ab-236">Log category</span></span>

<span data-ttu-id="565ab-237">Lorsqu’un `ILogger` objet est créé, une *catégorie* est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="565ab-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="565ab-238">Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="565ab-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="565ab-239">La chaîne de catégorie est arbitraire, mais la Convention consiste à utiliser le nom de la classe.</span><span class="sxs-lookup"><span data-stu-id="565ab-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="565ab-240">Par exemple, dans un contrôleur, le nom peut être `"TodoApi.Controllers.TodoController"` .</span><span class="sxs-lookup"><span data-stu-id="565ab-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="565ab-241">Les applications Web ASP.NET Core utilisent `ILogger<T>` pour recevoir automatiquement une `ILogger` instance qui utilise le nom de type qualifié complet de `T` comme catégorie :</span><span class="sxs-lookup"><span data-stu-id="565ab-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="565ab-242">Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="565ab-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="565ab-243">`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.</span><span class="sxs-lookup"><span data-stu-id="565ab-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="565ab-244">Log level</span><span class="sxs-lookup"><span data-stu-id="565ab-244">Log level</span></span>

<span data-ttu-id="565ab-245">Le tableau suivant répertorie les <xref:Microsoft.Extensions.Logging.LogLevel> valeurs, la `Log{LogLevel}` méthode d’extension de commodité et l’utilisation suggérée :</span><span class="sxs-lookup"><span data-stu-id="565ab-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="565ab-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="565ab-246">LogLevel</span></span> | <span data-ttu-id="565ab-247">Valeur</span><span class="sxs-lookup"><span data-stu-id="565ab-247">Value</span></span> | <span data-ttu-id="565ab-248">Méthode</span><span class="sxs-lookup"><span data-stu-id="565ab-248">Method</span></span> | <span data-ttu-id="565ab-249">Description</span><span class="sxs-lookup"><span data-stu-id="565ab-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="565ab-250">Trace</span><span class="sxs-lookup"><span data-stu-id="565ab-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="565ab-251">0</span><span class="sxs-lookup"><span data-stu-id="565ab-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="565ab-252">Contiennent les messages les plus détaillés.</span><span class="sxs-lookup"><span data-stu-id="565ab-252">Contain the most detailed messages.</span></span> <span data-ttu-id="565ab-253">Ces messages peuvent contenir des données d’application sensibles.</span><span class="sxs-lookup"><span data-stu-id="565ab-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="565ab-254">Ces messages sont désactivés par défaut et ***ne doivent pas*** être activés en production.</span><span class="sxs-lookup"><span data-stu-id="565ab-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="565ab-255">Déboguer</span><span class="sxs-lookup"><span data-stu-id="565ab-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="565ab-256">1</span><span class="sxs-lookup"><span data-stu-id="565ab-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="565ab-257">Pour le débogage et le développement.</span><span class="sxs-lookup"><span data-stu-id="565ab-257">For debugging and development.</span></span> <span data-ttu-id="565ab-258">Utilisez avec précaution en production en raison du volume élevé.</span><span class="sxs-lookup"><span data-stu-id="565ab-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="565ab-259">Informations</span><span class="sxs-lookup"><span data-stu-id="565ab-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="565ab-260">2</span><span class="sxs-lookup"><span data-stu-id="565ab-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="565ab-261">Effectue le suivi du déroulement général de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="565ab-262">Peut avoir une valeur à long terme.</span><span class="sxs-lookup"><span data-stu-id="565ab-262">May have long-term value.</span></span> |
| [<span data-ttu-id="565ab-263">Avertissement</span><span class="sxs-lookup"><span data-stu-id="565ab-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="565ab-264">3</span><span class="sxs-lookup"><span data-stu-id="565ab-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="565ab-265">Pour les événements anormaux ou inattendus.</span><span class="sxs-lookup"><span data-stu-id="565ab-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="565ab-266">Comprend généralement des erreurs ou des conditions qui ne provoquent pas l’échec de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="565ab-267">Error</span><span class="sxs-lookup"><span data-stu-id="565ab-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="565ab-268">4</span><span class="sxs-lookup"><span data-stu-id="565ab-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="565ab-269">Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées.</span><span class="sxs-lookup"><span data-stu-id="565ab-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="565ab-270">Ces messages indiquent un échec dans l’opération ou la demande en cours, et non dans l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="565ab-271">Critical</span><span class="sxs-lookup"><span data-stu-id="565ab-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="565ab-272">5</span><span class="sxs-lookup"><span data-stu-id="565ab-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="565ab-273">Fournit des informations sur des échecs qui nécessitent un examen immédiat.</span><span class="sxs-lookup"><span data-stu-id="565ab-273">For failures that require immediate attention.</span></span> <span data-ttu-id="565ab-274">Exemples : perte de données, espace disque insuffisant.</span><span class="sxs-lookup"><span data-stu-id="565ab-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="565ab-275">Aucun</span><span class="sxs-lookup"><span data-stu-id="565ab-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="565ab-276">6</span><span class="sxs-lookup"><span data-stu-id="565ab-276">6</span></span> | | <span data-ttu-id="565ab-277">Spécifie qu’une catégorie de journalisation ne doit écrire aucun message.</span><span class="sxs-lookup"><span data-stu-id="565ab-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="565ab-278">Dans le tableau précédent, la `LogLevel` est indiquée du niveau de gravité le plus bas au plus élevé.</span><span class="sxs-lookup"><span data-stu-id="565ab-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="565ab-279">Le premier paramètre de la méthode de [journalisation](xref:Microsoft.Extensions.Logging.LoggerExtensions) , <xref:Microsoft.Extensions.Logging.LogLevel> , indique la gravité du journal.</span><span class="sxs-lookup"><span data-stu-id="565ab-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="565ab-280">Au lieu d’appeler `Log(LogLevel, ...)` , la plupart des développeurs appellent les méthodes d’extension [log {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) .</span><span class="sxs-lookup"><span data-stu-id="565ab-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="565ab-281">Les `Log{LogLevel}` méthodes d’extension [appellent la méthode log et spécifient LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="565ab-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="565ab-282">Par exemple, les deux appels de journalisation suivants sont fonctionnellement équivalents et produisent le même journal :</span><span class="sxs-lookup"><span data-stu-id="565ab-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="565ab-283">`MyLogEvents.TestItem`est l’ID de l’événement.</span><span class="sxs-lookup"><span data-stu-id="565ab-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="565ab-284">`MyLogEvents`fait partie de l’exemple d’application et s’affiche dans la section [enregistrer l’ID d’événement](#leid) .</span><span class="sxs-lookup"><span data-stu-id="565ab-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="565ab-285">Le code suivant crée des journaux `Information` et `Warning` :</span><span class="sxs-lookup"><span data-stu-id="565ab-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="565ab-286">Dans le code précédent, le premier `Log{LogLevel}` paramètre, `MyLogEvents.GetItem` , est l' [ID d’événement du journal](#leid).</span><span class="sxs-lookup"><span data-stu-id="565ab-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="565ab-287">Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="565ab-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="565ab-288">Les paramètres de méthode sont expliqués dans la section [modèle de message](#lmt) , plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="565ab-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="565ab-289">Appelez la `Log{LogLevel}` méthode appropriée pour contrôler la quantité de sortie de journal écrite sur un support de stockage particulier.</span><span class="sxs-lookup"><span data-stu-id="565ab-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="565ab-290">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="565ab-290">For example:</span></span>

* <span data-ttu-id="565ab-291">En production :</span><span class="sxs-lookup"><span data-stu-id="565ab-291">In production:</span></span>
  * <span data-ttu-id="565ab-292">La journalisation au `Trace` niveau des ou de `Information` génère un volume important de messages journaux détaillés.</span><span class="sxs-lookup"><span data-stu-id="565ab-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="565ab-293">Pour contrôler les coûts et ne pas dépasser les limites de stockage des données, `Trace` `Information` les messages de journal et de niveau dans un magasin de données volumineux et à faible coût.</span><span class="sxs-lookup"><span data-stu-id="565ab-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="565ab-294">Envisagez `Trace` de limiter et `Information` à des catégories spécifiques.</span><span class="sxs-lookup"><span data-stu-id="565ab-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="565ab-295">La journalisation au `Warning` `Critical` niveau des niveaux doit produire peu de messages de journal.</span><span class="sxs-lookup"><span data-stu-id="565ab-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="565ab-296">Les coûts et les limites de stockage ne sont généralement pas un problème.</span><span class="sxs-lookup"><span data-stu-id="565ab-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="565ab-297">Certains journaux offrent davantage de flexibilité dans les choix de magasins de données.</span><span class="sxs-lookup"><span data-stu-id="565ab-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="565ab-298">En cours de développement :</span><span class="sxs-lookup"><span data-stu-id="565ab-298">In development:</span></span>
  * <span data-ttu-id="565ab-299">Défini sur `Warning`.</span><span class="sxs-lookup"><span data-stu-id="565ab-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="565ab-300">Ajoutez `Trace` `Information` des messages ou lors de la résolution des problèmes.</span><span class="sxs-lookup"><span data-stu-id="565ab-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="565ab-301">Pour limiter la sortie, `Trace` la définition ou `Information` uniquement pour les catégories en cours d’investigation.</span><span class="sxs-lookup"><span data-stu-id="565ab-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="565ab-302">ASP.NET Core écrit des journaux pour les événements de framework.</span><span class="sxs-lookup"><span data-stu-id="565ab-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="565ab-303">Par exemple, considérez la sortie du journal pour :</span><span class="sxs-lookup"><span data-stu-id="565ab-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="565ab-304">RazorApplication pages créée avec les modèles ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="565ab-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="565ab-305">Journalisation défini sur`Logging:Console:LogLevel:Microsoft:Information`</span><span class="sxs-lookup"><span data-stu-id="565ab-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="565ab-306">Navigation vers la page confidentialité :</span><span class="sxs-lookup"><span data-stu-id="565ab-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="565ab-307">Le code JSON suivant définit `Logging:Console:LogLevel:Microsoft:Information` :</span><span class="sxs-lookup"><span data-stu-id="565ab-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="565ab-308">ID d’événement de log</span><span class="sxs-lookup"><span data-stu-id="565ab-308">Log event ID</span></span>

<span data-ttu-id="565ab-309">Chaque journal peut spécifier un *ID d’événement*.</span><span class="sxs-lookup"><span data-stu-id="565ab-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="565ab-310">L’exemple d’application utilise la `MyLogEvents` classe pour définir les ID d’événement :</span><span class="sxs-lookup"><span data-stu-id="565ab-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="565ab-311">Un ID d’événement associe un jeu d’événements.</span><span class="sxs-lookup"><span data-stu-id="565ab-311">An event ID associates a set of events.</span></span> <span data-ttu-id="565ab-312">Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.</span><span class="sxs-lookup"><span data-stu-id="565ab-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="565ab-313">Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout.</span><span class="sxs-lookup"><span data-stu-id="565ab-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="565ab-314">Le fournisseur Debug n’affiche pas les ID d’événements.</span><span class="sxs-lookup"><span data-stu-id="565ab-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="565ab-315">Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :</span><span class="sxs-lookup"><span data-stu-id="565ab-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="565ab-316">Certains fournisseurs de journalisation stockent l’ID d’événement dans un champ, ce qui permet de filtrer l’ID.</span><span class="sxs-lookup"><span data-stu-id="565ab-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="565ab-317">Modèle de message de journal</span><span class="sxs-lookup"><span data-stu-id="565ab-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="565ab-318">Chaque API de journal utilise un modèle de message.</span><span class="sxs-lookup"><span data-stu-id="565ab-318">Each log API uses a message template.</span></span> <span data-ttu-id="565ab-319">Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis.</span><span class="sxs-lookup"><span data-stu-id="565ab-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="565ab-320">Utilisez des noms et non des nombres pour les espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="565ab-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="565ab-321">L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="565ab-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="565ab-322">Dans le code suivant, les noms de paramètres sont hors séquence dans le modèle de message :</span><span class="sxs-lookup"><span data-stu-id="565ab-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="565ab-323">Le code précédent crée un message de journal avec les valeurs de paramètre dans l’ordre :</span><span class="sxs-lookup"><span data-stu-id="565ab-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="565ab-324">Cette approche permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique ou structurée](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span><span class="sxs-lookup"><span data-stu-id="565ab-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="565ab-325">Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="565ab-326">Cela permet aux fournisseurs de journalisation de stocker les valeurs de paramètres en tant que champs.</span><span class="sxs-lookup"><span data-stu-id="565ab-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="565ab-327">Par exemple, considérez la méthode d’enregistreur d’événements suivante :</span><span class="sxs-lookup"><span data-stu-id="565ab-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="565ab-328">Par exemple, lors de la journalisation dans le stockage table Azure :</span><span class="sxs-lookup"><span data-stu-id="565ab-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="565ab-329">Chaque entité de table Azure peut avoir des `ID` `RequestTime` Propriétés et.</span><span class="sxs-lookup"><span data-stu-id="565ab-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="565ab-330">Les tables avec des propriétés simplifient les requêtes sur les données journalisées.</span><span class="sxs-lookup"><span data-stu-id="565ab-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="565ab-331">Par exemple, une requête peut trouver tous les journaux d’une `RequestTime` plage donnée sans avoir à analyser le délai d’attente du message texte.</span><span class="sxs-lookup"><span data-stu-id="565ab-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="565ab-332">Journaliser les exceptions</span><span class="sxs-lookup"><span data-stu-id="565ab-332">Log exceptions</span></span>

<span data-ttu-id="565ab-333">Les méthodes d’enregistreur d’événements ont des surcharges qui prennent un paramètre d’exception :</span><span class="sxs-lookup"><span data-stu-id="565ab-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="565ab-334">La journalisation des exceptions est spécifique au fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="565ab-335">Niveau de journalisation par défaut</span><span class="sxs-lookup"><span data-stu-id="565ab-335">Default log level</span></span>

<span data-ttu-id="565ab-336">Si le niveau de journalisation par défaut n’est pas défini, la valeur par défaut du niveau de journal est `Information` .</span><span class="sxs-lookup"><span data-stu-id="565ab-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="565ab-337">Par exemple, considérez l’application Web suivante :</span><span class="sxs-lookup"><span data-stu-id="565ab-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="565ab-338">Créé avec les modèles d’application Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="565ab-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="565ab-339">*appsettings.jssur* et *appsettings.Development.jssur* supprimé ou renommé.</span><span class="sxs-lookup"><span data-stu-id="565ab-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="565ab-340">Avec la configuration précédente, la navigation vers la page confidentialité ou d’origine génère de nombreux `Trace` `Debug` messages, et `Information` avec `Microsoft` dans le nom de catégorie.</span><span class="sxs-lookup"><span data-stu-id="565ab-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="565ab-341">Le code suivant définit le niveau de journalisation par défaut lorsque le niveau de journalisation par défaut n’est pas défini dans la configuration :</span><span class="sxs-lookup"><span data-stu-id="565ab-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="565ab-342">En règle générale, les niveaux de journalisation doivent être spécifiés dans la configuration et non dans le code.</span><span class="sxs-lookup"><span data-stu-id="565ab-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="565ab-343">fonction Filter</span><span class="sxs-lookup"><span data-stu-id="565ab-343">Filter function</span></span>

<span data-ttu-id="565ab-344">Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels aucune règle n’est assignée par la configuration ou le code :</span><span class="sxs-lookup"><span data-stu-id="565ab-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="565ab-345">Le code précédent affiche les journaux de console lorsque la catégorie contient `Controller` ou `Microsoft` et que le niveau de journal est `Information` ou supérieur.</span><span class="sxs-lookup"><span data-stu-id="565ab-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="565ab-346">En règle générale, les niveaux de journalisation doivent être spécifiés dans la configuration et non dans le code.</span><span class="sxs-lookup"><span data-stu-id="565ab-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="565ab-347">Catégories ASP.NET Core et EF Core</span><span class="sxs-lookup"><span data-stu-id="565ab-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="565ab-348">Le tableau suivant contient des catégories utilisées par ASP.NET Core et Entity Framework Core, avec des remarques sur les journaux :</span><span class="sxs-lookup"><span data-stu-id="565ab-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="565ab-349">Category</span><span class="sxs-lookup"><span data-stu-id="565ab-349">Category</span></span>                            | <span data-ttu-id="565ab-350">Notes</span><span class="sxs-lookup"><span data-stu-id="565ab-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="565ab-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="565ab-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="565ab-352">Diagnostics ASP.NET Core généraux.</span><span class="sxs-lookup"><span data-stu-id="565ab-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="565ab-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="565ab-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="565ab-354">Liste des clés considérées, trouvées et utilisées.</span><span class="sxs-lookup"><span data-stu-id="565ab-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="565ab-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="565ab-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="565ab-356">Hôtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="565ab-356">Hosts allowed.</span></span> |
| <span data-ttu-id="565ab-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="565ab-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="565ab-358">Temps de traitement des requêtes HTTP et heure de démarrage.</span><span class="sxs-lookup"><span data-stu-id="565ab-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="565ab-359">Liste des assemblys de démarrage d’hébergement chargés.</span><span class="sxs-lookup"><span data-stu-id="565ab-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="565ab-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="565ab-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="565ab-361">MVC et Razor Diagnostics.</span><span class="sxs-lookup"><span data-stu-id="565ab-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="565ab-362">Liaison de données, exécution de filtres, compilation de vues, sélection d’actions.</span><span class="sxs-lookup"><span data-stu-id="565ab-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="565ab-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="565ab-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="565ab-364">Informations de correspondance des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="565ab-364">Route matching information.</span></span> |
| <span data-ttu-id="565ab-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="565ab-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="565ab-366">Démarrage et arrêt de la connexion, et réponses persistantes.</span><span class="sxs-lookup"><span data-stu-id="565ab-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="565ab-367">Informations du certificat HTTPS.</span><span class="sxs-lookup"><span data-stu-id="565ab-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="565ab-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="565ab-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="565ab-369">Fichiers pris en charge.</span><span class="sxs-lookup"><span data-stu-id="565ab-369">Files served.</span></span> |
| <span data-ttu-id="565ab-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="565ab-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="565ab-371">Diagnostics Entity Framework Core généraux.</span><span class="sxs-lookup"><span data-stu-id="565ab-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="565ab-372">Activité et configuration de la base de données, détection des modifications, migrations.</span><span class="sxs-lookup"><span data-stu-id="565ab-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="565ab-373">Pour afficher plus de catégories dans la fenêtre de la console, définissez **appsettings.Development.jssur** ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="565ab-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="565ab-374">Étendues de journal</span><span class="sxs-lookup"><span data-stu-id="565ab-374">Log scopes</span></span>

 <span data-ttu-id="565ab-375">Une *étendue* peut regrouper un ensemble d’opérations logiques.</span><span class="sxs-lookup"><span data-stu-id="565ab-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="565ab-376">Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble.</span><span class="sxs-lookup"><span data-stu-id="565ab-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="565ab-377">Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.</span><span class="sxs-lookup"><span data-stu-id="565ab-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="565ab-378">Une étendue :</span><span class="sxs-lookup"><span data-stu-id="565ab-378">A scope:</span></span>

* <span data-ttu-id="565ab-379">Est un <xref:System.IDisposable> type qui est retourné par la <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> méthode.</span><span class="sxs-lookup"><span data-stu-id="565ab-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="565ab-380">Dure jusqu’à ce qu’il soit supprimé.</span><span class="sxs-lookup"><span data-stu-id="565ab-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="565ab-381">Les fournisseurs suivants prennent en charge les étendues :</span><span class="sxs-lookup"><span data-stu-id="565ab-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="565ab-382">AzureAppServicesFile et AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="565ab-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="565ab-383">Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :</span><span class="sxs-lookup"><span data-stu-id="565ab-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="565ab-384">Le code JSON suivant active des étendues pour le fournisseur de console :</span><span class="sxs-lookup"><span data-stu-id="565ab-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="565ab-385">Le code suivant active les étendues pour le fournisseur Console :</span><span class="sxs-lookup"><span data-stu-id="565ab-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="565ab-386">En règle générale, la journalisation doit être spécifiée dans la configuration et non dans le code.</span><span class="sxs-lookup"><span data-stu-id="565ab-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="565ab-387">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="565ab-387">Built-in logging providers</span></span>

<span data-ttu-id="565ab-388">ASP.NET Core comprend les fournisseurs de journalisation suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="565ab-389">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-389">Console</span></span>](#console)
* [<span data-ttu-id="565ab-390">Déboguer</span><span class="sxs-lookup"><span data-stu-id="565ab-390">Debug</span></span>](#debug)
* [<span data-ttu-id="565ab-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="565ab-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="565ab-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="565ab-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="565ab-393">AzureAppServicesFile et AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="565ab-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="565ab-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="565ab-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="565ab-395">Pour plus d’informations sur `stdout` et le débogage de la journalisation avec le Module ASP.net Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .</span><span class="sxs-lookup"><span data-stu-id="565ab-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="565ab-396">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-396">Console</span></span>

<span data-ttu-id="565ab-397">Le `Console` fournisseur journalise la sortie dans la console.</span><span class="sxs-lookup"><span data-stu-id="565ab-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="565ab-398">Pour plus d’informations sur `Console` l’affichage des journaux dans le développement, consultez [journalisation de la sortie de dotnet Run et de Visual Studio](#dnrvs).</span><span class="sxs-lookup"><span data-stu-id="565ab-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="565ab-399">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-399">Debug</span></span>

<span data-ttu-id="565ab-400">Le `Debug` fournisseur écrit la sortie du journal à l’aide de la classe [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) .</span><span class="sxs-lookup"><span data-stu-id="565ab-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="565ab-401">Appelle pour `System.Diagnostics.Debug.WriteLine` écrire dans le `Debug` fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="565ab-402">Sur Linux, l' `Debug` emplacement du journal du fournisseur est dépendant de la distribution et peut être l’un des suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="565ab-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="565ab-403">*/var/log/message*</span></span>
* <span data-ttu-id="565ab-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="565ab-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="565ab-405">Source de l’événement</span><span class="sxs-lookup"><span data-stu-id="565ab-405">Event Source</span></span>

<span data-ttu-id="565ab-406">Le `EventSource` fournisseur écrit dans une source d’événements multiplateforme portant le nom `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="565ab-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="565ab-407">Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="565ab-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="565ab-408">outils de trace dotnet</span><span class="sxs-lookup"><span data-stu-id="565ab-408">dotnet trace tooling</span></span>

<span data-ttu-id="565ab-409">L’outil [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) est un outil global de l’interface CLI multiplateforme qui permet la collecte des traces .net core d’un processus en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="565ab-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="565ab-410">L’outil collecte les <xref:Microsoft.Extensions.Logging.EventSource> données du fournisseur à l’aide d’un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .</span><span class="sxs-lookup"><span data-stu-id="565ab-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="565ab-411">Consultez [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) pour obtenir des instructions d’installation.</span><span class="sxs-lookup"><span data-stu-id="565ab-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="565ab-412">Utilisez les outils de trace dotnet pour collecter une trace à partir d’une application :</span><span class="sxs-lookup"><span data-stu-id="565ab-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="565ab-413">Exécutez l’application avec la `dotnet run` commande.</span><span class="sxs-lookup"><span data-stu-id="565ab-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="565ab-414">Déterminez l’identificateur de processus (PID) de l’application .NET Core :</span><span class="sxs-lookup"><span data-stu-id="565ab-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="565ab-415">Sur Windows, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="565ab-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="565ab-416">Gestionnaire des tâches (Ctrl + Alt + Suppr)</span><span class="sxs-lookup"><span data-stu-id="565ab-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="565ab-417">TaskList, commande</span><span class="sxs-lookup"><span data-stu-id="565ab-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="565ab-418">Commande PowerShell d’extraction de processus</span><span class="sxs-lookup"><span data-stu-id="565ab-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="565ab-419">Sur Linux, utilisez la [commande pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="565ab-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="565ab-420">Recherchez le PID pour le processus qui porte le même nom que l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="565ab-421">Exécutez la `dotnet trace` commande.</span><span class="sxs-lookup"><span data-stu-id="565ab-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="565ab-422">Syntaxe de la commande générale :</span><span class="sxs-lookup"><span data-stu-id="565ab-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="565ab-423">Quand vous utilisez une interface de commande PowerShell, placez la `--providers` valeur entre guillemets simples ( `'` ) :</span><span class="sxs-lookup"><span data-stu-id="565ab-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="565ab-424">Sur les plateformes non-Windows, ajoutez l' `-f speedscope` option permettant de modifier le format du fichier de trace de sortie en `speedscope` .</span><span class="sxs-lookup"><span data-stu-id="565ab-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="565ab-425">Mot clé</span><span class="sxs-lookup"><span data-stu-id="565ab-425">Keyword</span></span> | <span data-ttu-id="565ab-426">Description</span><span class="sxs-lookup"><span data-stu-id="565ab-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="565ab-427">1</span><span class="sxs-lookup"><span data-stu-id="565ab-427">1</span></span>       | <span data-ttu-id="565ab-428">Consignez les événements méta sur le `LoggingEventSource` .</span><span class="sxs-lookup"><span data-stu-id="565ab-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="565ab-429">N’enregistre pas les événements à partir de `ILogger` ).</span><span class="sxs-lookup"><span data-stu-id="565ab-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="565ab-430">2</span><span class="sxs-lookup"><span data-stu-id="565ab-430">2</span></span>       | <span data-ttu-id="565ab-431">Active l' `Message` événement lorsque `ILogger.Log()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="565ab-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="565ab-432">Fournit des informations d’une façon de programmation (non mise en forme).</span><span class="sxs-lookup"><span data-stu-id="565ab-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="565ab-433">4</span><span class="sxs-lookup"><span data-stu-id="565ab-433">4</span></span>       | <span data-ttu-id="565ab-434">Active l' `FormatMessage` événement lorsque `ILogger.Log()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="565ab-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="565ab-435">Fournit la version de chaîne mise en forme des informations.</span><span class="sxs-lookup"><span data-stu-id="565ab-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="565ab-436">8</span><span class="sxs-lookup"><span data-stu-id="565ab-436">8</span></span>       | <span data-ttu-id="565ab-437">Active l' `MessageJson` événement lorsque `ILogger.Log()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="565ab-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="565ab-438">Fournit une représentation JSON des arguments.</span><span class="sxs-lookup"><span data-stu-id="565ab-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="565ab-439">Niveau d'événement</span><span class="sxs-lookup"><span data-stu-id="565ab-439">Event Level</span></span> | <span data-ttu-id="565ab-440">Description</span><span class="sxs-lookup"><span data-stu-id="565ab-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="565ab-441">0</span><span class="sxs-lookup"><span data-stu-id="565ab-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="565ab-442">1</span><span class="sxs-lookup"><span data-stu-id="565ab-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="565ab-443">2</span><span class="sxs-lookup"><span data-stu-id="565ab-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="565ab-444">3</span><span class="sxs-lookup"><span data-stu-id="565ab-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="565ab-445">4</span><span class="sxs-lookup"><span data-stu-id="565ab-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="565ab-446">5</span><span class="sxs-lookup"><span data-stu-id="565ab-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="565ab-447">`FilterSpecs`les entrées pour `{Logger Category}` et `{Event Level}` représentent des conditions de filtrage de journal supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="565ab-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="565ab-448">Séparez `FilterSpecs` les entrées par un point-virgule ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="565ab-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="565ab-449">Exemple utilisant un interpréteur de commandes Windows (**sans** guillemets simples autour de la `--providers` valeur) :</span><span class="sxs-lookup"><span data-stu-id="565ab-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="565ab-450">La commande précédente active :</span><span class="sxs-lookup"><span data-stu-id="565ab-450">The preceding command activates:</span></span>

   * <span data-ttu-id="565ab-451">Enregistreur d’événements de la source d’événements pour produire des chaînes mises en forme ( `4` ) pour les erreurs ( `2` ).</span><span class="sxs-lookup"><span data-stu-id="565ab-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="565ab-452">`Microsoft.AspNetCore.Hosting`journalisation au `Informational` niveau de la journalisation ( `4` ).</span><span class="sxs-lookup"><span data-stu-id="565ab-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="565ab-453">Arrêtez les outils de trace dotnet en appuyant sur la touche entrée ou Ctrl + C.</span><span class="sxs-lookup"><span data-stu-id="565ab-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="565ab-454">La trace est enregistrée avec le nom *trace. NetTrace* dans le dossier où la `dotnet trace` commande est exécutée.</span><span class="sxs-lookup"><span data-stu-id="565ab-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="565ab-455">Ouvrez la trace avec [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="565ab-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="565ab-456">Ouvrez le fichier *trace. NetTrace* et explorez les événements de trace.</span><span class="sxs-lookup"><span data-stu-id="565ab-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="565ab-457">Si l’application ne crée pas l’hôte avec `CreateDefaultBuilder` , ajoutez le fournisseur de la [source d’événements](#event-source-provider) à la configuration de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="565ab-458">Pour plus d’informations, consultez :</span><span class="sxs-lookup"><span data-stu-id="565ab-458">For more information, see:</span></span>

* <span data-ttu-id="565ab-459">[Utilitaire trace for Performance Analysis (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentation .net Core)</span><span class="sxs-lookup"><span data-stu-id="565ab-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="565ab-460">[Utilitaire trace for Performance Analysis (dotnet-trace) (documentation sur le](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) référentiel GitHub dotnet/Diagnostics)</span><span class="sxs-lookup"><span data-stu-id="565ab-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="565ab-461">[LoggingEventSource, classe](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (Explorateur d’API .net)</span><span class="sxs-lookup"><span data-stu-id="565ab-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="565ab-462">[Source de référence LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): pour obtenir la source de référence pour une version différente, remplacez la branche par `release/{Version}` , où `{Version}` est la version de ASP.net Core souhaitée.</span><span class="sxs-lookup"><span data-stu-id="565ab-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="565ab-463">[Perfview](#perfview): utile pour l’affichage des traces de la source d’événements.</span><span class="sxs-lookup"><span data-stu-id="565ab-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="565ab-464">Perfview</span><span class="sxs-lookup"><span data-stu-id="565ab-464">Perfview</span></span>

<span data-ttu-id="565ab-465">Utilisez l' [utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux.</span><span class="sxs-lookup"><span data-stu-id="565ab-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="565ab-466">Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="565ab-467">Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**.</span><span class="sxs-lookup"><span data-stu-id="565ab-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="565ab-468">Ne manquez pas le `*` au début de la chaîne.</span><span class="sxs-lookup"><span data-stu-id="565ab-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="565ab-469">Journal des événements Windows</span><span class="sxs-lookup"><span data-stu-id="565ab-469">Windows EventLog</span></span>

<span data-ttu-id="565ab-470">Le `EventLog` fournisseur envoie la sortie de journal dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="565ab-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="565ab-471">Contrairement aux autres fournisseurs, le `EventLog` fournisseur n’hérite ***pas*** des paramètres non-fournisseur par défaut.</span><span class="sxs-lookup"><span data-stu-id="565ab-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="565ab-472">Si `EventLog` les paramètres du journal ne sont pas spécifiés, ils utilisent [par défaut LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span><span class="sxs-lookup"><span data-stu-id="565ab-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="565ab-473">Pour consigner les événements inférieurs à <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , définissez explicitement le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="565ab-474">L’exemple suivant définit le niveau de journalisation par défaut du journal des événements sur <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="565ab-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="565ab-475">Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) peuvent passer <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> .</span><span class="sxs-lookup"><span data-stu-id="565ab-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="565ab-476">Si `null` ou n’est pas spécifié, les paramètres par défaut suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="565ab-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="565ab-477">`LogName`: « Application »</span><span class="sxs-lookup"><span data-stu-id="565ab-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="565ab-478">`SourceName`: « .NET Runtime »</span><span class="sxs-lookup"><span data-stu-id="565ab-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="565ab-479">`MachineName`: Le nom de l’ordinateur local est utilisé.</span><span class="sxs-lookup"><span data-stu-id="565ab-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="565ab-480">Le code suivant remplace la `SourceName` valeur par défaut de par `".NET Runtime"` `MyLogs` :</span><span class="sxs-lookup"><span data-stu-id="565ab-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="565ab-481">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="565ab-481">Azure App Service</span></span>

<span data-ttu-id="565ab-482">Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="565ab-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="565ab-483">Le fournisseur de package n’est pas inclus dans le framework partagé.</span><span class="sxs-lookup"><span data-stu-id="565ab-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="565ab-484">Pour utiliser le fournisseur, ajoutez le package du fournisseur au projet.</span><span class="sxs-lookup"><span data-stu-id="565ab-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="565ab-485">Pour configurer les paramètres du fournisseur, utilisez <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> et <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="565ab-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="565ab-486">Lors du déploiement sur Azure App Service, l’application utilise les paramètres de la section [app service les journaux](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) de la page **app service** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="565ab-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="565ab-487">Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="565ab-488">**Journalisation des applications (système de fichiers)**</span><span class="sxs-lookup"><span data-stu-id="565ab-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="565ab-489">**Journalisation des applications (objet blob)**</span><span class="sxs-lookup"><span data-stu-id="565ab-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="565ab-490">L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*.</span><span class="sxs-lookup"><span data-stu-id="565ab-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="565ab-491">La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2.</span><span class="sxs-lookup"><span data-stu-id="565ab-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="565ab-492">Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="565ab-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="565ab-493">Ce fournisseur se connecte uniquement lorsque le projet s’exécute dans l’environnement Azure.</span><span class="sxs-lookup"><span data-stu-id="565ab-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="565ab-494">Streaming des journaux Azure</span><span class="sxs-lookup"><span data-stu-id="565ab-494">Azure log streaming</span></span>

<span data-ttu-id="565ab-495">Azure log streaming prend en charge l’affichage de l’activité des journaux en temps réel à partir de :</span><span class="sxs-lookup"><span data-stu-id="565ab-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="565ab-496">Serveur d'applications</span><span class="sxs-lookup"><span data-stu-id="565ab-496">The app server</span></span>
* <span data-ttu-id="565ab-497">Serveur web</span><span class="sxs-lookup"><span data-stu-id="565ab-497">The web server</span></span>
* <span data-ttu-id="565ab-498">Suivi des demandes ayant échoué</span><span class="sxs-lookup"><span data-stu-id="565ab-498">Failed request tracing</span></span>

<span data-ttu-id="565ab-499">Pour configurer le streaming des journaux Azure :</span><span class="sxs-lookup"><span data-stu-id="565ab-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="565ab-500">Accédez à la page **journaux App service** à partir de la page du portail de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="565ab-501">Définissez **Journal des applications (Système de fichiers)** sur **Activé**.</span><span class="sxs-lookup"><span data-stu-id="565ab-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="565ab-502">Choisissez le **niveau** du journal.</span><span class="sxs-lookup"><span data-stu-id="565ab-502">Choose the log **Level**.</span></span> <span data-ttu-id="565ab-503">Ce paramètre s’applique uniquement à la diffusion en continu de journaux Azure.</span><span class="sxs-lookup"><span data-stu-id="565ab-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="565ab-504">Accédez à la page **flux de journal** pour afficher les journaux.</span><span class="sxs-lookup"><span data-stu-id="565ab-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="565ab-505">Les messages journalisés sont enregistrés avec l' `ILogger` interface.</span><span class="sxs-lookup"><span data-stu-id="565ab-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="565ab-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="565ab-506">Azure Application Insights</span></span>

<span data-ttu-id="565ab-507">Le package du fournisseur [Microsoft. extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit les journaux dans [Azure application Insights](/azure/azure-monitor/app/cloudservices).</span><span class="sxs-lookup"><span data-stu-id="565ab-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="565ab-508">Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie.</span><span class="sxs-lookup"><span data-stu-id="565ab-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="565ab-509">Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.</span><span class="sxs-lookup"><span data-stu-id="565ab-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="565ab-510">Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="565ab-511">Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="565ab-512">Le package [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) est destiné à ASP.net 4. x, et non ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="565ab-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="565ab-513">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="565ab-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="565ab-514">Vue d’ensemble d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="565ab-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="565ab-515">[Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="565ab-516">[Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="565ab-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="565ab-517">[Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="565ab-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="565ab-518">[Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="565ab-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="565ab-519">Fournisseurs de journalisation tiers</span><span class="sxs-lookup"><span data-stu-id="565ab-519">Third-party logging providers</span></span>

<span data-ttu-id="565ab-520">Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="565ab-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="565ab-521">[elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="565ab-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="565ab-523">[JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="565ab-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="565ab-524">[KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="565ab-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="565ab-525">[Log4net](https://logging.apache.org/log4net/) ([GitHub référentiel](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="565ab-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="565ab-526">[Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="565ab-527">[NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="565ab-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub référentiel](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="565ab-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="565ab-529">[Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="565ab-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="565ab-530">[Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="565ab-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="565ab-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="565ab-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="565ab-532">Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="565ab-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="565ab-533">L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :</span><span class="sxs-lookup"><span data-stu-id="565ab-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="565ab-534">Ajoutez un package NuGet à votre projet.</span><span class="sxs-lookup"><span data-stu-id="565ab-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="565ab-535">Appeler une `ILoggerFactory` méthode d’extension fournie par le Framework de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="565ab-536">Pour plus d’informations, consultez la documentation de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="565ab-537">Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="565ab-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="565ab-538">Application console non-hôte</span><span class="sxs-lookup"><span data-stu-id="565ab-538">Non-host console app</span></span>

<span data-ttu-id="565ab-539">Pour obtenir un exemple d’utilisation de l’hôte générique dans une application de console non Web, consultez le fichier *Program.cs* de l' [exemple d’application tâches en arrière-plan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).</span><span class="sxs-lookup"><span data-stu-id="565ab-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="565ab-540">Le code de journalisation pour les applications sans hôte générique diffère dans la façon dont les [fournisseurs sont ajoutés](#add-providers) et les [enregistreurs d'événements créés](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="565ab-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="565ab-541">Fournisseurs de journalisation</span><span class="sxs-lookup"><span data-stu-id="565ab-541">Logging providers</span></span>

<span data-ttu-id="565ab-542">Dans une application de console non hôte, appelez la méthode d’extension `Add{provider name}` du fournisseur lors de la création d’un `LoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="565ab-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="565ab-543">Créer des journaux</span><span class="sxs-lookup"><span data-stu-id="565ab-543">Create logs</span></span>

<span data-ttu-id="565ab-544">Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="565ab-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="565ab-545">Utilisez `LoggerFactory` pour créer un `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="565ab-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="565ab-546">L’exemple suivant crée un enregistreur d’événements avec `LoggingConsoleApp.Program` comme catégorie.</span><span class="sxs-lookup"><span data-stu-id="565ab-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="565ab-547">Dans les exemples de base ASP.NET suivants, l’enregistreur d’événements est utilisé pour créer des journaux avec `Information` comme niveau.</span><span class="sxs-lookup"><span data-stu-id="565ab-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="565ab-548">Le *niveau* du journal indique la gravité de l’événement consigné.</span><span class="sxs-lookup"><span data-stu-id="565ab-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="565ab-549">Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail dans ce document.</span><span class="sxs-lookup"><span data-stu-id="565ab-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="565ab-550">Journal lors de la construction de l’hôte</span><span class="sxs-lookup"><span data-stu-id="565ab-550">Log during host construction</span></span>

<span data-ttu-id="565ab-551">La journalisation pendant la construction de l’hôte n’est pas prise en charge directement.</span><span class="sxs-lookup"><span data-stu-id="565ab-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="565ab-552">Toutefois, un enregistreur d’événements distinct peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="565ab-552">However, a separate logger can be used.</span></span> <span data-ttu-id="565ab-553">Dans l’exemple suivant, un enregistreur d’événements [Serilog](https://serilog.net/) est utilisé pour se connecter `CreateHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="565ab-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="565ab-554">`AddSerilog`utilise la configuration statique spécifiée dans `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="565ab-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="565ab-555">Configurer un service qui dépend de ILogger</span><span class="sxs-lookup"><span data-stu-id="565ab-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="565ab-556">L’injection de constructeur d’un enregistreur d’événements dans `Startup` fonctionne dans les versions antérieures d’ASP.NET Core, car un conteneur d’injection de dépendances distinct est créé pour l’hôte web.</span><span class="sxs-lookup"><span data-stu-id="565ab-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="565ab-557">Pour plus d’informations sur la raison de la création d’un seul conteneur pour l’hôte générique, consultez l’[annonce de changement cassant](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="565ab-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="565ab-558">Pour configurer un service qui dépend de `ILogger<T>` , utilisez l’injection de constructeur ou fournissez une méthode de fabrique.</span><span class="sxs-lookup"><span data-stu-id="565ab-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="565ab-559">L’approche de la méthode de fabrique est recommandée uniquement s’il n’y a aucune autre option.</span><span class="sxs-lookup"><span data-stu-id="565ab-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="565ab-560">Par exemple, considérez un service qui a besoin d’une `ILogger<T>` instance fournie par di :</span><span class="sxs-lookup"><span data-stu-id="565ab-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="565ab-561">Le code en surbrillance précédent est un [Func](/dotnet/api/system.func-2) qui s’exécute la première fois que le conteneur di doit construire une instance de `MyService` .</span><span class="sxs-lookup"><span data-stu-id="565ab-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="565ab-562">Vous pouvez accéder à tous les services inscrits de cette manière.</span><span class="sxs-lookup"><span data-stu-id="565ab-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="565ab-563">Créer des journaux dans main</span><span class="sxs-lookup"><span data-stu-id="565ab-563">Create logs in Main</span></span>

<span data-ttu-id="565ab-564">Le code suivant se connecte `Main` en obtenant une `ILogger` instance à partir de di après avoir généré l’hôte :</span><span class="sxs-lookup"><span data-stu-id="565ab-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="565ab-565">Créer des journaux au démarrage</span><span class="sxs-lookup"><span data-stu-id="565ab-565">Create logs in Startup</span></span>

<span data-ttu-id="565ab-566">Le code suivant écrit les journaux dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="565ab-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="565ab-567">L’écriture de journaux avant la fin de l’installation du conteneur d’injection de dépendances dans la méthode `Startup.ConfigureServices` n’est pas prise en charge :</span><span class="sxs-lookup"><span data-stu-id="565ab-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="565ab-568">L’injection d’un enregistreur d’événements dans le constructeur `Startup` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="565ab-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="565ab-569">L’injection d’un enregistreur d’événements dans la signature de méthode `Startup.ConfigureServices` n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="565ab-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="565ab-570">La raison de cette restriction est que la journalisation dépend de l’injection de dépendances et de la configuration qui, à son tour, dépend de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="565ab-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="565ab-571">Le conteneur d’injection de dépendances n’est pas configuré avant que `ConfigureServices` soit terminé.</span><span class="sxs-lookup"><span data-stu-id="565ab-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="565ab-572">Pour plus d’informations sur la configuration d’un service qui dépend de `ILogger<T>` ou de la raison pour laquelle l’injection d’un enregistreur d’événements dans les `Startup` versions antérieures, consultez [configurer un service qui dépend de ILogger](#csdi)</span><span class="sxs-lookup"><span data-stu-id="565ab-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="565ab-573">Aucune méthode d’enregistreur d’événements asynchrone</span><span class="sxs-lookup"><span data-stu-id="565ab-573">No asynchronous logger methods</span></span>

<span data-ttu-id="565ab-574">La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="565ab-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="565ab-575">Si un magasin de données de journalisation est lent, n’écrivez pas directement dessus.</span><span class="sxs-lookup"><span data-stu-id="565ab-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="565ab-576">Envisagez d’écrire les messages du journal dans un magasin rapide, puis de les déplacer ultérieurement vers la Banque lente.</span><span class="sxs-lookup"><span data-stu-id="565ab-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="565ab-577">Par exemple, lorsque vous vous connectez à SQL Server, ne le faites pas directement dans une `Log` méthode, puisque les `Log` méthodes sont synchrones.</span><span class="sxs-lookup"><span data-stu-id="565ab-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="565ab-578">Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server.</span><span class="sxs-lookup"><span data-stu-id="565ab-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="565ab-579">Pour plus d’informations, consultez [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problème github.</span><span class="sxs-lookup"><span data-stu-id="565ab-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="565ab-580">Modifier les niveaux de journal dans une application en cours d’exécution</span><span class="sxs-lookup"><span data-stu-id="565ab-580">Change log levels in a running app</span></span>

<span data-ttu-id="565ab-581">L’API de journalisation n’inclut pas de scénario pour modifier les niveaux de journal lorsqu’une application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="565ab-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="565ab-582">Toutefois, certains fournisseurs de configuration sont en charge du rechargement de la configuration, ce qui prend immédiatement effet sur la configuration de la journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="565ab-583">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider), recharge la configuration de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="565ab-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="565ab-584">Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="565ab-585">ILogger et ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="565ab-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="565ab-586">Les <xref:Microsoft.Extensions.Logging.ILogger%601> <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces et et les implémentations sont incluses dans la kit SDK .net core.</span><span class="sxs-lookup"><span data-stu-id="565ab-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="565ab-587">Ils sont également disponibles dans les packages NuGet suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="565ab-588">Les interfaces se trouvent dans [Microsoft. extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="565ab-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="565ab-589">Les implémentations par défaut se trouvent dans [Microsoft. extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="565ab-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="565ab-590">Appliquer les règles de filtre de journal dans le code</span><span class="sxs-lookup"><span data-stu-id="565ab-590">Apply log filter rules in code</span></span>

<span data-ttu-id="565ab-591">L’approche recommandée pour définir des règles de filtre de journal consiste à utiliser la [configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="565ab-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="565ab-592">L'exemple suivant montre comment enregistrer des règles de filtre dans le code :</span><span class="sxs-lookup"><span data-stu-id="565ab-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="565ab-593">`logging.AddFilter("System", LogLevel.Debug)`spécifie la `System` catégorie et le niveau de journalisation `Debug` .</span><span class="sxs-lookup"><span data-stu-id="565ab-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="565ab-594">Le filtre est appliqué à tous les fournisseurs, car un fournisseur spécifique n’a pas été configuré.</span><span class="sxs-lookup"><span data-stu-id="565ab-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="565ab-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`indiquant</span><span class="sxs-lookup"><span data-stu-id="565ab-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="565ab-596">`Debug`Fournisseur de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="565ab-597">Niveau de journalisation `Information` et supérieur.</span><span class="sxs-lookup"><span data-stu-id="565ab-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="565ab-598">Toutes les catégories commençant par `"Microsoft"` .</span><span class="sxs-lookup"><span data-stu-id="565ab-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="565ab-599">Créer un enregistreur d’événements personnalisé</span><span class="sxs-lookup"><span data-stu-id="565ab-599">Create a custom logger</span></span>

<span data-ttu-id="565ab-600">Pour ajouter un enregistreur d’événements personnalisé, ajoutez un <xref:Microsoft.Extensions.Logging.ILoggerProvider> avec <xref:Microsoft.Extensions.Logging.ILoggerFactory> :</span><span class="sxs-lookup"><span data-stu-id="565ab-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="565ab-601">Le `ILoggerProvider` crée une ou plusieurs `ILogger` instances.</span><span class="sxs-lookup"><span data-stu-id="565ab-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="565ab-602">Les `ILogger` instances sont utilisées par le Framework pour enregistrer les informations.</span><span class="sxs-lookup"><span data-stu-id="565ab-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="565ab-603">Exemple de configuration d’un enregistreur d’événements personnalisé</span><span class="sxs-lookup"><span data-stu-id="565ab-603">Sample custom logger configuration</span></span>

<span data-ttu-id="565ab-604">L'exemple :</span><span class="sxs-lookup"><span data-stu-id="565ab-604">The sample:</span></span>

* <span data-ttu-id="565ab-605">Est conçu pour être un exemple très basique qui définit la couleur de la console du journal par l’ID d’événement et le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="565ab-606">Les journaux ne sont généralement pas modifiés par l’ID d’événement et ne sont pas spécifiques au niveau du journal.</span><span class="sxs-lookup"><span data-stu-id="565ab-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="565ab-607">Crée différentes entrées de console couleur par niveau de journalisation et ID d’événement en utilisant le type de configuration suivant :</span><span class="sxs-lookup"><span data-stu-id="565ab-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="565ab-608">Le code précédent affecte au niveau par défaut la valeur `Warning` et à la couleur `Yellow` .</span><span class="sxs-lookup"><span data-stu-id="565ab-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="565ab-609">Si le `EventId` est défini sur 0, tous les événements seront journalisés.</span><span class="sxs-lookup"><span data-stu-id="565ab-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="565ab-610">Créer l’enregistreur d’événements personnalisé</span><span class="sxs-lookup"><span data-stu-id="565ab-610">Create the custom logger</span></span>

<span data-ttu-id="565ab-611">Le `ILogger` nom de catégorie d’implémentation correspond généralement à la source de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="565ab-612">Par exemple, le type dans lequel l’enregistreur d’événements est créé :</span><span class="sxs-lookup"><span data-stu-id="565ab-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="565ab-613">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="565ab-613">The preceding code:</span></span>

* <span data-ttu-id="565ab-614">Crée une instance d’enregistreur d’événements par nom de catégorie.</span><span class="sxs-lookup"><span data-stu-id="565ab-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="565ab-615">Archive `logLevel == _config.LogLevel` `IsEnabled` , donc chaque `logLevel` a un enregistreur d’événements unique.</span><span class="sxs-lookup"><span data-stu-id="565ab-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="565ab-616">En règle générale, les enregistreurs d’événements doivent également être activés pour tous les niveaux de journalisation les plus élevés :</span><span class="sxs-lookup"><span data-stu-id="565ab-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="565ab-617">Créer le LoggerProvider personnalisé</span><span class="sxs-lookup"><span data-stu-id="565ab-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="565ab-618">`LoggerProvider`Est la classe qui crée les instances d’enregistreur d’événements.</span><span class="sxs-lookup"><span data-stu-id="565ab-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="565ab-619">Il n’est peut-être pas nécessaire de créer une instance d’enregistreur d’événements par catégorie, mais cela est logique pour certains enregistreurs d’événements, tels que NLog ou log4net.</span><span class="sxs-lookup"><span data-stu-id="565ab-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="565ab-620">En procédant ainsi, vous pouvez choisir différentes cibles de sortie de journalisation par catégorie, si nécessaire :</span><span class="sxs-lookup"><span data-stu-id="565ab-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="565ab-621">Dans le code précédent, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> crée une seule instance de `ColorConsoleLogger` par nom de catégorie et la stocke dans le [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) ;</span><span class="sxs-lookup"><span data-stu-id="565ab-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="565ab-622">Utilisation et inscription de l’enregistreur d’événements personnalisé</span><span class="sxs-lookup"><span data-stu-id="565ab-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="565ab-623">Inscrire l’enregistreur d’événements dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="565ab-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="565ab-624">Pour le code précédent, fournissez au moins une méthode d’extension pour le `ILoggerFactory` :</span><span class="sxs-lookup"><span data-stu-id="565ab-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="565ab-625">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="565ab-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="565ab-626">Les bogues de journalisation doivent être créés dans le référentiel [github.com/dotnet/Runtime/](https://github.com/dotnet/runtime/issues) .</span><span class="sxs-lookup"><span data-stu-id="565ab-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="565ab-627">Par [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="565ab-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="565ab-628">.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés.</span><span class="sxs-lookup"><span data-stu-id="565ab-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="565ab-629">Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.</span><span class="sxs-lookup"><span data-stu-id="565ab-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="565ab-630">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="565ab-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="565ab-631">Ajouter des fournisseurs</span><span class="sxs-lookup"><span data-stu-id="565ab-631">Add providers</span></span>

<span data-ttu-id="565ab-632">Un fournisseur de journalisation affiche ou stocke des journaux.</span><span class="sxs-lookup"><span data-stu-id="565ab-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="565ab-633">Par exemple, le fournisseur Console affiche les journaux dans la console, et le fournisseur Azure Application Insights les stocke dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="565ab-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="565ab-634">Il est possible d’envoyer les journaux à plusieurs endroits en ajoutant plusieurs fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="565ab-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="565ab-635">Pour ajouter un fournisseur, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="565ab-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="565ab-636">Le code précédent nécessite des références à `Microsoft.Extensions.Logging` et `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="565ab-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="565ab-637">Le modèle de projet par défaut appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="565ab-638">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-638">Console</span></span>
* <span data-ttu-id="565ab-639">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-639">Debug</span></span>
* <span data-ttu-id="565ab-640">EventSource (à partir d’ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="565ab-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="565ab-641">Si vous utilisez `CreateDefaultBuilder`, vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix.</span><span class="sxs-lookup"><span data-stu-id="565ab-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="565ab-642">Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.</span><span class="sxs-lookup"><span data-stu-id="565ab-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="565ab-643">Vous trouverez des informations sur les [fournisseurs de journalisation intégrés](#built-in-logging-providers) et les [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="565ab-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="565ab-644">Créer des journaux</span><span class="sxs-lookup"><span data-stu-id="565ab-644">Create logs</span></span>

<span data-ttu-id="565ab-645">Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="565ab-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="565ab-646">Dans une application web ou un service hébergé, obtenez un `ILogger` à partir de l’injection de dépendances (DI).</span><span class="sxs-lookup"><span data-stu-id="565ab-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="565ab-647">Dans les applications de console non hôtes, utilisez le `LoggerFactory` pour créer un `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="565ab-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="565ab-648">L’exemple d’ASP.NET Core suivant crée un enregistreur d’événements de catégorie `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="565ab-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="565ab-649">La *catégorie* du journal est une chaîne associée à chaque journal.</span><span class="sxs-lookup"><span data-stu-id="565ab-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="565ab-650">L’instance `ILogger<T>` fournie par l’injection de dépendances crée des journaux ayant comme catégorie un nom complet de type `T`.</span><span class="sxs-lookup"><span data-stu-id="565ab-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="565ab-651">Dans les exemples d’ASP.NET Core et d’application de console suivants, l’enregistreur d’événements est utilisé pour créer des journaux de niveau `Information`.</span><span class="sxs-lookup"><span data-stu-id="565ab-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="565ab-652">Le *niveau* du journal indique la gravité de l’événement consigné.</span><span class="sxs-lookup"><span data-stu-id="565ab-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="565ab-653">Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans cet article.</span><span class="sxs-lookup"><span data-stu-id="565ab-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="565ab-654">Créer des journaux au démarrage</span><span class="sxs-lookup"><span data-stu-id="565ab-654">Create logs in Startup</span></span>

<span data-ttu-id="565ab-655">Pour écrire des journaux dans la classe `Startup`, ajoutez un paramètre `ILogger` dans la signature de constructeur :</span><span class="sxs-lookup"><span data-stu-id="565ab-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="565ab-656">Créer des journaux dans la classe Programme</span><span class="sxs-lookup"><span data-stu-id="565ab-656">Create logs in the Program class</span></span>

<span data-ttu-id="565ab-657">Pour écrire des journaux dans la classe `Program`, récupérez une instance `ILogger` auprès de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="565ab-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="565ab-658">La journalisation pendant la construction de l’hôte n’est pas prise en charge directement.</span><span class="sxs-lookup"><span data-stu-id="565ab-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="565ab-659">Toutefois, un enregistreur d’événements distinct peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="565ab-659">However, a separate logger can be used.</span></span> <span data-ttu-id="565ab-660">Dans l’exemple suivant, un enregistreur d’événements [Serilog](https://serilog.net/) est utilisé pour se connecter `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="565ab-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="565ab-661">`AddSerilog`utilise la configuration statique spécifiée dans `Log.Logger` :</span><span class="sxs-lookup"><span data-stu-id="565ab-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="565ab-662">Aucune méthode d’enregistreur d’événements asynchrone</span><span class="sxs-lookup"><span data-stu-id="565ab-662">No asynchronous logger methods</span></span>

<span data-ttu-id="565ab-663">La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances.</span><span class="sxs-lookup"><span data-stu-id="565ab-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="565ab-664">Si votre magasin de données de journalisation est lent, n’écrivez pas directement dedans.</span><span class="sxs-lookup"><span data-stu-id="565ab-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="565ab-665">Écrivez au départ les messages de journal dans un magasin rapide, puis déplacez-les dans le magasin lent.</span><span class="sxs-lookup"><span data-stu-id="565ab-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="565ab-666">Par exemple, si vous vous connectez à SQL Server, évitez de le faire directement dans une méthode `Log`, car les méthodes `Log` sont synchrones.</span><span class="sxs-lookup"><span data-stu-id="565ab-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="565ab-667">Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server.</span><span class="sxs-lookup"><span data-stu-id="565ab-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="565ab-668">Pour plus d’informations, consultez [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problème github.</span><span class="sxs-lookup"><span data-stu-id="565ab-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="565ab-669">Configuration</span><span class="sxs-lookup"><span data-stu-id="565ab-669">Configuration</span></span>

<span data-ttu-id="565ab-670">La configuration de fournisseur de journalisation est fournie par un ou plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="565ab-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="565ab-671">Formats de fichiers (INI, JSON et XML).</span><span class="sxs-lookup"><span data-stu-id="565ab-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="565ab-672">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="565ab-672">Command-line arguments.</span></span>
* <span data-ttu-id="565ab-673">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="565ab-673">Environment variables.</span></span>
* <span data-ttu-id="565ab-674">Objets .NET en mémoire.</span><span class="sxs-lookup"><span data-stu-id="565ab-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="565ab-675">Stockage [Secret Manager](xref:security/app-secrets) non chiffré.</span><span class="sxs-lookup"><span data-stu-id="565ab-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="565ab-676">Magasin utilisateur chiffré comme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="565ab-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="565ab-677">Fournisseurs personnalisés (installés ou créés).</span><span class="sxs-lookup"><span data-stu-id="565ab-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="565ab-678">Par exemple, la configuration de journalisation est généralement fournie par la section `Logging` des fichiers de paramètres d’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="565ab-679">L’exemple suivant montre le contenu d’un fichier *appsettings.Development.json* standard :</span><span class="sxs-lookup"><span data-stu-id="565ab-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="565ab-680">La propriété `Logging` peut avoir un niveau `LogLevel` et des propriétés de module fournisseur d'informations (Console ici).</span><span class="sxs-lookup"><span data-stu-id="565ab-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="565ab-681">La propriété `LogLevel` sous `Logging` spécifie le [niveau](#log-level) de journalisation minimal pour les catégories sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="565ab-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="565ab-682">Dans l’exemple, les catégories `System` et `Microsoft` sont consignées au niveau `Information`, et toutes les autres au niveau `Debug`.</span><span class="sxs-lookup"><span data-stu-id="565ab-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="565ab-683">Les autres propriétés sous `Logging` spécifient les fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="565ab-684">Cet exemple concerne le fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="565ab-684">The example is for the Console provider.</span></span> <span data-ttu-id="565ab-685">Si un fournisseur prend en charge les [étendues de journal](#log-scopes), `IncludeScopes` indique s’ils sont activés.</span><span class="sxs-lookup"><span data-stu-id="565ab-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="565ab-686">Une propriété de fournisseur (comme `Console` dans l’exemple) peut également spécifier une propriété `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="565ab-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="565ab-687">`LogLevel` sous un fournisseur spécifie les niveaux à consigner pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="565ab-688">Si les niveaux sont spécifiés dans `Logging.{providername}.LogLevel`, ils remplacent ce qui est défini dans `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="565ab-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="565ab-689">Considérons, par exemple, le code JSON suivant :</span><span class="sxs-lookup"><span data-stu-id="565ab-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="565ab-690">Dans le code JSON précédent, les `Console` paramètres du fournisseur remplacent le niveau de journalisation précédent (par défaut).</span><span class="sxs-lookup"><span data-stu-id="565ab-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="565ab-691">L’API de journalisation n’inclut pas de scénario pour modifier les niveaux de journal lorsqu’une application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="565ab-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="565ab-692">Toutefois, certains fournisseurs de configuration sont en charge du rechargement de la configuration, ce qui prend immédiatement effet sur la configuration de la journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="565ab-693">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider), qui est ajouté par `CreateDefaultBuilder` pour lire les fichiers de paramètres, recharge la configuration de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="565ab-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="565ab-694">Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="565ab-695">Pour plus d’informations sur l’implémentation des fournisseurs de configuration, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="565ab-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="565ab-696">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="565ab-696">Sample logging output</span></span>

<span data-ttu-id="565ab-697">Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console lorsque l’application est exécutée en ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="565ab-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="565ab-698">Voici un exemple de sortie de la console :</span><span class="sxs-lookup"><span data-stu-id="565ab-698">Here's an example of console output:</span></span>

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

<span data-ttu-id="565ab-699">Les journaux précédents ont été générés par une requête HTTP Get à l’exemple d’application à l’adresse `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="565ab-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="565ab-700">Voici un exemple des mêmes journaux tels qu’ils s’affichent dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="565ab-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="565ab-701">Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApi ».</span><span class="sxs-lookup"><span data-stu-id="565ab-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="565ab-702">Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="565ab-703">ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="565ab-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="565ab-704">Les autres sections de cet article détaillent certains points et présentent les options de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="565ab-705">Packages NuGet</span><span class="sxs-lookup"><span data-stu-id="565ab-705">NuGet packages</span></span>

<span data-ttu-id="565ab-706">Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="565ab-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="565ab-707">Catégorie de journal</span><span class="sxs-lookup"><span data-stu-id="565ab-707">Log category</span></span>

<span data-ttu-id="565ab-708">Quand un objet `ILogger` est créé, une *catégorie* lui est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="565ab-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="565ab-709">Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="565ab-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="565ab-710">Si la catégorie peut être n’importe quelle chaîne, la convention est d’utiliser le nom de la classe, par exemple « TodoApi.Controllers.TodoController ».</span><span class="sxs-lookup"><span data-stu-id="565ab-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="565ab-711">Utilisez `ILogger<T>` pour obtenir une instance de `ILogger` qui utilise le nom de type complet `T` comme catégorie :</span><span class="sxs-lookup"><span data-stu-id="565ab-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="565ab-712">Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :</span><span class="sxs-lookup"><span data-stu-id="565ab-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="565ab-713">`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.</span><span class="sxs-lookup"><span data-stu-id="565ab-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="565ab-714">Log level</span><span class="sxs-lookup"><span data-stu-id="565ab-714">Log level</span></span>

<span data-ttu-id="565ab-715">Chaque journal spécifie une valeur <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="565ab-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="565ab-716">Le niveau de journalisation indique la gravité ou l’importance.</span><span class="sxs-lookup"><span data-stu-id="565ab-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="565ab-717">Vous pourriez par exemple écrire un journal `Information` lorsqu’une méthode se termine normalement et un journal `Warning` lorsqu’une méthode retourne un code de statut *404 Not Found*.</span><span class="sxs-lookup"><span data-stu-id="565ab-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="565ab-718">Le code suivant crée des journaux `Information` et `Warning` :</span><span class="sxs-lookup"><span data-stu-id="565ab-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="565ab-719">Dans le code précédent, les `MyLogEvents.GetItem` `MyLogEvents.GetItemNotFound` paramètres et sont l' [ID d’événement du journal](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="565ab-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="565ab-720">Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="565ab-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="565ab-721">Les paramètres de méthode sont expliqués dans la [section modèle de message de journal](#lmt) de cet article.</span><span class="sxs-lookup"><span data-stu-id="565ab-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="565ab-722">Les méthodes de journal qui comportent le niveau dans leur nom (par exemple, `LogInformation` et `LogWarning`) sont des [méthodes d’extension pour ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="565ab-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="565ab-723">Elles appellent une méthode `Log` qui prend un paramètre `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="565ab-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="565ab-724">Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe.</span><span class="sxs-lookup"><span data-stu-id="565ab-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="565ab-725">Pour plus d’informations, voir <xref:Microsoft.Extensions.Logging.ILogger> et le [code source des extensions d’enregistreur d'événements](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="565ab-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="565ab-726">ASP.NET Core définit les niveaux de journalisation suivants, classés selon leur degré de gravité (du plus faible au plus élevé).</span><span class="sxs-lookup"><span data-stu-id="565ab-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="565ab-727">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="565ab-727">Trace = 0</span></span>

  <span data-ttu-id="565ab-728">Informations en général exclusivement utiles à des fins de débogage.</span><span class="sxs-lookup"><span data-stu-id="565ab-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="565ab-729">Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="565ab-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="565ab-730">*Désactivé par défaut.*</span><span class="sxs-lookup"><span data-stu-id="565ab-730">*Disabled by default.*</span></span>

* <span data-ttu-id="565ab-731">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="565ab-731">Debug = 1</span></span>

  <span data-ttu-id="565ab-732">Informations qui peuvent être utiles dans le développement et le débogage.</span><span class="sxs-lookup"><span data-stu-id="565ab-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="565ab-733">Exemple : `Entering method Configure with flag set to true.` En raison de leur volume élevé, n’activez les journaux de niveau `Debug` en production que pour résoudre des problèmes.</span><span class="sxs-lookup"><span data-stu-id="565ab-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="565ab-734">Information = 2</span><span class="sxs-lookup"><span data-stu-id="565ab-734">Information = 2</span></span>

  <span data-ttu-id="565ab-735">Informations de suivi du flux général de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="565ab-736">Ces journaux ont généralement une utilité à long terme.</span><span class="sxs-lookup"><span data-stu-id="565ab-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="565ab-737">Exemple : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="565ab-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="565ab-738">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="565ab-738">Warning = 3</span></span>

  <span data-ttu-id="565ab-739">Informations sur les événements anormaux ou inattendus dans le flux de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="565ab-740">Il peut s’agir d’erreurs ou d’autres situations qui ne provoquent pas l’arrêt de l’application, mais qu’il peut être intéressant d’examiner.</span><span class="sxs-lookup"><span data-stu-id="565ab-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="565ab-741">Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées.</span><span class="sxs-lookup"><span data-stu-id="565ab-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="565ab-742">Exemple : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="565ab-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="565ab-743">Error = 4</span><span class="sxs-lookup"><span data-stu-id="565ab-743">Error = 4</span></span>

  <span data-ttu-id="565ab-744">Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées.</span><span class="sxs-lookup"><span data-stu-id="565ab-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="565ab-745">Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="565ab-746">Exemple de message de journal : `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="565ab-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="565ab-747">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="565ab-747">Critical = 5</span></span>

  <span data-ttu-id="565ab-748">Fournit des informations sur des échecs qui nécessitent un examen immédiat.</span><span class="sxs-lookup"><span data-stu-id="565ab-748">For failures that require immediate attention.</span></span> <span data-ttu-id="565ab-749">Exemples : perte de données, espace disque insuffisant.</span><span class="sxs-lookup"><span data-stu-id="565ab-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="565ab-750">Le niveau de journalisation permet de contrôler le volume de la sortie de journal écrite sur un support de stockage ou dans une fenêtre d’affichage.</span><span class="sxs-lookup"><span data-stu-id="565ab-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="565ab-751">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="565ab-751">For example:</span></span>

* <span data-ttu-id="565ab-752">En production :</span><span class="sxs-lookup"><span data-stu-id="565ab-752">In production:</span></span>
  * <span data-ttu-id="565ab-753">La journalisation `Trace` au `Information` niveau des niveaux de production génère un volume important de messages journaux détaillés.</span><span class="sxs-lookup"><span data-stu-id="565ab-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="565ab-754">Pour contrôler les coûts et ne pas dépasser les limites de stockage des données, consignez les `Trace` `Information` messages de niveau dans un magasin de données volumineux et à faible coût.</span><span class="sxs-lookup"><span data-stu-id="565ab-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="565ab-755">La journalisation au niveau `Warning` `Critical` des niveaux produit généralement moins de messages journaux plus petits.</span><span class="sxs-lookup"><span data-stu-id="565ab-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="565ab-756">Par conséquent, les coûts et les limites de stockage ne sont généralement pas un problème, ce qui se traduit par une plus grande flexibilité du choix des magasins de données.</span><span class="sxs-lookup"><span data-stu-id="565ab-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="565ab-757">Lors du développement :</span><span class="sxs-lookup"><span data-stu-id="565ab-757">During development:</span></span>
  * <span data-ttu-id="565ab-758">Journalisation `Warning` `Critical` des messages dans la console.</span><span class="sxs-lookup"><span data-stu-id="565ab-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="565ab-759">Ajoutez `Trace` `Information` des messages lors de la résolution des problèmes.</span><span class="sxs-lookup"><span data-stu-id="565ab-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="565ab-760">La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="565ab-761">ASP.NET Core écrit des journaux pour les événements de framework.</span><span class="sxs-lookup"><span data-stu-id="565ab-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="565ab-762">Aucun journal du niveau `Debug` ou `Trace` n’était créé dans les exemples de journaux présentés plus haut dans cet article, puisque les journaux au-dessous du niveau `Information` étaient exclus.</span><span class="sxs-lookup"><span data-stu-id="565ab-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="565ab-763">Voici un exemple de journaux Console produits par l’exemple d’application configurée pour afficher les journaux `Debug` :</span><span class="sxs-lookup"><span data-stu-id="565ab-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="565ab-764">ID d’événement de log</span><span class="sxs-lookup"><span data-stu-id="565ab-764">Log event ID</span></span>

<span data-ttu-id="565ab-765">Chaque journal peut spécifier un *ID d’événement*.</span><span class="sxs-lookup"><span data-stu-id="565ab-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="565ab-766">Pour cela, l’exemple d’application utilise une classe `LoggingEvents` définie localement :</span><span class="sxs-lookup"><span data-stu-id="565ab-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="565ab-767">Un ID d’événement associe un jeu d’événements.</span><span class="sxs-lookup"><span data-stu-id="565ab-767">An event ID associates a set of events.</span></span> <span data-ttu-id="565ab-768">Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.</span><span class="sxs-lookup"><span data-stu-id="565ab-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="565ab-769">Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout.</span><span class="sxs-lookup"><span data-stu-id="565ab-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="565ab-770">Le fournisseur Debug n’affiche pas les ID d’événements.</span><span class="sxs-lookup"><span data-stu-id="565ab-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="565ab-771">Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :</span><span class="sxs-lookup"><span data-stu-id="565ab-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="565ab-772">Modèle de message de journal</span><span class="sxs-lookup"><span data-stu-id="565ab-772">Log message template</span></span>

<span data-ttu-id="565ab-773">Chaque journal spécifie un modèle de message.</span><span class="sxs-lookup"><span data-stu-id="565ab-773">Each log specifies a message template.</span></span> <span data-ttu-id="565ab-774">Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis.</span><span class="sxs-lookup"><span data-stu-id="565ab-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="565ab-775">Utilisez des noms et non des nombres pour les espaces réservés.</span><span class="sxs-lookup"><span data-stu-id="565ab-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="565ab-776">L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="565ab-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="565ab-777">Dans le code suivant, on voit que les noms de paramètres sont hors séquence dans le modèle de message :</span><span class="sxs-lookup"><span data-stu-id="565ab-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="565ab-778">Ce code crée un message de journal avec les valeurs des paramètres dans la séquence :</span><span class="sxs-lookup"><span data-stu-id="565ab-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="565ab-779">Le framework de journalisation fonctionne ainsi pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="565ab-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="565ab-780">Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="565ab-781">C’est grâce à ces informations que les fournisseurs de journalisation peuvent stocker les valeurs des paramètres sous forme de champs.</span><span class="sxs-lookup"><span data-stu-id="565ab-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="565ab-782">Supposons par exemple que les appels de méthodes d’enregistreur d’événements se présentent ainsi :</span><span class="sxs-lookup"><span data-stu-id="565ab-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="565ab-783">Si vous envoyez les journaux au Stockage Table Azure, chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journaux.</span><span class="sxs-lookup"><span data-stu-id="565ab-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="565ab-784">Une requête peut rechercher tous les journaux compris dans une plage `RequestTime` spécifique sans analyser le délai d’expiration du message texte.</span><span class="sxs-lookup"><span data-stu-id="565ab-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="565ab-785">Journalisation des exceptions</span><span class="sxs-lookup"><span data-stu-id="565ab-785">Logging exceptions</span></span>

<span data-ttu-id="565ab-786">Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="565ab-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="565ab-787">Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon.</span><span class="sxs-lookup"><span data-stu-id="565ab-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="565ab-788">Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.</span><span class="sxs-lookup"><span data-stu-id="565ab-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="565ab-789">Filtrage de journal</span><span class="sxs-lookup"><span data-stu-id="565ab-789">Log filtering</span></span>

<span data-ttu-id="565ab-790">Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories.</span><span class="sxs-lookup"><span data-stu-id="565ab-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="565ab-791">Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés.</span><span class="sxs-lookup"><span data-stu-id="565ab-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="565ab-792">Pour supprimer tous les journaux, choisissez `LogLevel.None` comme niveau de journalisation minimal.</span><span class="sxs-lookup"><span data-stu-id="565ab-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="565ab-793">La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="565ab-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="565ab-794">Créer des règles de filtre dans la configuration</span><span class="sxs-lookup"><span data-stu-id="565ab-794">Create filter rules in configuration</span></span>

<span data-ttu-id="565ab-795">Le code du modèle de projet appelle `CreateDefaultBuilder` pour configurer la journalisation des fournisseurs de console, de débogage et EventSource (ASP.NET Core 2,2 ou version ultérieure).</span><span class="sxs-lookup"><span data-stu-id="565ab-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="565ab-796">La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, conformément à ce qui a été expliqué [plus haut dans cet article](#configuration).</span><span class="sxs-lookup"><span data-stu-id="565ab-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="565ab-797">Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="565ab-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="565ab-798">Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une pour tous les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="565ab-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="565ab-799">Une seule règle est choisie pour chaque fournisseur à la création d’un objet `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="565ab-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="565ab-800">Règles de filtre dans le code</span><span class="sxs-lookup"><span data-stu-id="565ab-800">Filter rules in code</span></span>

<span data-ttu-id="565ab-801">L'exemple suivant montre comment enregistrer des règles de filtre dans le code :</span><span class="sxs-lookup"><span data-stu-id="565ab-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="565ab-802">Le second `AddFilter` spécifie le fournisseur Debug par son nom de type.</span><span class="sxs-lookup"><span data-stu-id="565ab-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="565ab-803">Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="565ab-804">Mode d’application des règles de filtre</span><span class="sxs-lookup"><span data-stu-id="565ab-804">How filtering rules are applied</span></span>

<span data-ttu-id="565ab-805">Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="565ab-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="565ab-806">Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.</span><span class="sxs-lookup"><span data-stu-id="565ab-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="565ab-807">Number</span><span class="sxs-lookup"><span data-stu-id="565ab-807">Number</span></span> | <span data-ttu-id="565ab-808">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="565ab-808">Provider</span></span>      | <span data-ttu-id="565ab-809">Catégories commençant par...</span><span class="sxs-lookup"><span data-stu-id="565ab-809">Categories that begin with ...</span></span>          | <span data-ttu-id="565ab-810">Niveau de journalisation minimum</span><span class="sxs-lookup"><span data-stu-id="565ab-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="565ab-811">1</span><span class="sxs-lookup"><span data-stu-id="565ab-811">1</span></span>      | <span data-ttu-id="565ab-812">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-812">Debug</span></span>         | <span data-ttu-id="565ab-813">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="565ab-813">All categories</span></span>                          | <span data-ttu-id="565ab-814">Information</span><span class="sxs-lookup"><span data-stu-id="565ab-814">Information</span></span>       |
| <span data-ttu-id="565ab-815">2</span><span class="sxs-lookup"><span data-stu-id="565ab-815">2</span></span>      | <span data-ttu-id="565ab-816">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-816">Console</span></span>       | <span data-ttu-id="565ab-817">Microsoft. AspNetCore. Mvc. Razor . Intérieurs</span><span class="sxs-lookup"><span data-stu-id="565ab-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="565ab-818">Avertissement</span><span class="sxs-lookup"><span data-stu-id="565ab-818">Warning</span></span>           |
| <span data-ttu-id="565ab-819">3</span><span class="sxs-lookup"><span data-stu-id="565ab-819">3</span></span>      | <span data-ttu-id="565ab-820">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-820">Console</span></span>       | <span data-ttu-id="565ab-821">Microsoft. AspNetCore. Mvc. Razor .Razor</span><span class="sxs-lookup"><span data-stu-id="565ab-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="565ab-822">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-822">Debug</span></span>             |
| <span data-ttu-id="565ab-823">4</span><span class="sxs-lookup"><span data-stu-id="565ab-823">4</span></span>      | <span data-ttu-id="565ab-824">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-824">Console</span></span>       | <span data-ttu-id="565ab-825">Microsoft. AspNetCore. Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="565ab-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="565ab-826">Error</span><span class="sxs-lookup"><span data-stu-id="565ab-826">Error</span></span>             |
| <span data-ttu-id="565ab-827">5</span><span class="sxs-lookup"><span data-stu-id="565ab-827">5</span></span>      | <span data-ttu-id="565ab-828">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-828">Console</span></span>       | <span data-ttu-id="565ab-829">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="565ab-829">All categories</span></span>                          | <span data-ttu-id="565ab-830">Information</span><span class="sxs-lookup"><span data-stu-id="565ab-830">Information</span></span>       |
| <span data-ttu-id="565ab-831">6</span><span class="sxs-lookup"><span data-stu-id="565ab-831">6</span></span>      | <span data-ttu-id="565ab-832">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="565ab-832">All providers</span></span> | <span data-ttu-id="565ab-833">Toutes les catégories</span><span class="sxs-lookup"><span data-stu-id="565ab-833">All categories</span></span>                          | <span data-ttu-id="565ab-834">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-834">Debug</span></span>             |
| <span data-ttu-id="565ab-835">7</span><span class="sxs-lookup"><span data-stu-id="565ab-835">7</span></span>      | <span data-ttu-id="565ab-836">Tous les fournisseurs</span><span class="sxs-lookup"><span data-stu-id="565ab-836">All providers</span></span> | <span data-ttu-id="565ab-837">Système</span><span class="sxs-lookup"><span data-stu-id="565ab-837">System</span></span>                                  | <span data-ttu-id="565ab-838">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-838">Debug</span></span>             |
| <span data-ttu-id="565ab-839">8</span><span class="sxs-lookup"><span data-stu-id="565ab-839">8</span></span>      | <span data-ttu-id="565ab-840">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-840">Debug</span></span>         | <span data-ttu-id="565ab-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="565ab-841">Microsoft</span></span>                               | <span data-ttu-id="565ab-842">Trace</span><span class="sxs-lookup"><span data-stu-id="565ab-842">Trace</span></span>             |

<span data-ttu-id="565ab-843">À la création d’un objet `ILogger`, l’objet `ILoggerFactory` sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="565ab-844">Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="565ab-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="565ab-845">La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.</span><span class="sxs-lookup"><span data-stu-id="565ab-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="565ab-846">L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :</span><span class="sxs-lookup"><span data-stu-id="565ab-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="565ab-847">Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias.</span><span class="sxs-lookup"><span data-stu-id="565ab-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="565ab-848">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.</span><span class="sxs-lookup"><span data-stu-id="565ab-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="565ab-849">À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant.</span><span class="sxs-lookup"><span data-stu-id="565ab-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="565ab-850">Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.</span><span class="sxs-lookup"><span data-stu-id="565ab-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="565ab-851">Si plusieurs règles sont sélectionnées, prenez la **dernière**.</span><span class="sxs-lookup"><span data-stu-id="565ab-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="565ab-852">Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="565ab-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="565ab-853">Avec la liste de règles ci-dessus, supposons que vous créez un `ILogger` objet pour la catégorie «Microsoft. AspNetCore. Mvc. Razor Razor Moteur " :</span><span class="sxs-lookup"><span data-stu-id="565ab-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="565ab-854">Les règles 1, 6 et 8 s’appliquent au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="565ab-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="565ab-855">La règle 8 est sélectionnée, car c’est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="565ab-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="565ab-856">Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="565ab-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="565ab-857">La règle 3 est la plus spécifique.</span><span class="sxs-lookup"><span data-stu-id="565ab-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="565ab-858">L’instance `ILogger` ainsi produite envoie des journaux de niveau `Trace` ou supérieur au fournisseur Debug.</span><span class="sxs-lookup"><span data-stu-id="565ab-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="565ab-859">Les journaux de niveau `Debug` et supérieurs sont envoyés au fournisseur Console.</span><span class="sxs-lookup"><span data-stu-id="565ab-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="565ab-860">Alias de fournisseur</span><span class="sxs-lookup"><span data-stu-id="565ab-860">Provider aliases</span></span>

<span data-ttu-id="565ab-861">Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.</span><span class="sxs-lookup"><span data-stu-id="565ab-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="565ab-862">Pour les fournisseurs intégrés, utilisez les alias suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="565ab-863">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-863">Console</span></span>
* <span data-ttu-id="565ab-864">Débogage</span><span class="sxs-lookup"><span data-stu-id="565ab-864">Debug</span></span>
* <span data-ttu-id="565ab-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="565ab-865">EventSource</span></span>
* <span data-ttu-id="565ab-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="565ab-866">EventLog</span></span>
* <span data-ttu-id="565ab-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="565ab-867">TraceSource</span></span>
* <span data-ttu-id="565ab-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="565ab-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="565ab-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="565ab-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="565ab-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="565ab-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="565ab-871">Niveau minimum par défaut</span><span class="sxs-lookup"><span data-stu-id="565ab-871">Default minimum level</span></span>

<span data-ttu-id="565ab-872">Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique.</span><span class="sxs-lookup"><span data-stu-id="565ab-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="565ab-873">L’exemple suivant montre comment définir le niveau minimum :</span><span class="sxs-lookup"><span data-stu-id="565ab-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="565ab-874">Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="565ab-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="565ab-875">Fonctions de filtrage</span><span class="sxs-lookup"><span data-stu-id="565ab-875">Filter functions</span></span>

<span data-ttu-id="565ab-876">Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle.</span><span class="sxs-lookup"><span data-stu-id="565ab-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="565ab-877">Le code de la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="565ab-878">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="565ab-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="565ab-879">Niveaux et les catégories de système</span><span class="sxs-lookup"><span data-stu-id="565ab-879">System categories and levels</span></span>

<span data-ttu-id="565ab-880">Voici quelques catégories utilisées par ASP.NET Core et Entity Framework Core, avec des notes sur les journaux associés :</span><span class="sxs-lookup"><span data-stu-id="565ab-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="565ab-881">Category</span><span class="sxs-lookup"><span data-stu-id="565ab-881">Category</span></span>                            | <span data-ttu-id="565ab-882">Notes</span><span class="sxs-lookup"><span data-stu-id="565ab-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="565ab-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="565ab-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="565ab-884">Diagnostics ASP.NET Core généraux.</span><span class="sxs-lookup"><span data-stu-id="565ab-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="565ab-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="565ab-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="565ab-886">Liste des clés considérées, trouvées et utilisées.</span><span class="sxs-lookup"><span data-stu-id="565ab-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="565ab-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="565ab-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="565ab-888">Hôtes autorisés.</span><span class="sxs-lookup"><span data-stu-id="565ab-888">Hosts allowed.</span></span> |
| <span data-ttu-id="565ab-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="565ab-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="565ab-890">Temps de traitement des requêtes HTTP et heure de démarrage.</span><span class="sxs-lookup"><span data-stu-id="565ab-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="565ab-891">Liste des assemblys de démarrage d’hébergement chargés.</span><span class="sxs-lookup"><span data-stu-id="565ab-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="565ab-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="565ab-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="565ab-893">MVC et Razor Diagnostics.</span><span class="sxs-lookup"><span data-stu-id="565ab-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="565ab-894">Liaison de données, exécution de filtres, compilation de vues, sélection d’actions.</span><span class="sxs-lookup"><span data-stu-id="565ab-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="565ab-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="565ab-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="565ab-896">Informations de correspondance des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="565ab-896">Route matching information.</span></span> |
| <span data-ttu-id="565ab-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="565ab-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="565ab-898">Démarrage et arrêt de la connexion, et réponses persistantes.</span><span class="sxs-lookup"><span data-stu-id="565ab-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="565ab-899">Informations du certificat HTTPS.</span><span class="sxs-lookup"><span data-stu-id="565ab-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="565ab-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="565ab-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="565ab-901">Fichiers pris en charge.</span><span class="sxs-lookup"><span data-stu-id="565ab-901">Files served.</span></span> |
| <span data-ttu-id="565ab-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="565ab-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="565ab-903">Diagnostics Entity Framework Core généraux.</span><span class="sxs-lookup"><span data-stu-id="565ab-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="565ab-904">Activité et configuration de la base de données, détection des modifications, migrations.</span><span class="sxs-lookup"><span data-stu-id="565ab-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="565ab-905">Étendues de journal</span><span class="sxs-lookup"><span data-stu-id="565ab-905">Log scopes</span></span>

 <span data-ttu-id="565ab-906">Une *étendue* peut regrouper un ensemble d’opérations logiques.</span><span class="sxs-lookup"><span data-stu-id="565ab-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="565ab-907">Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble.</span><span class="sxs-lookup"><span data-stu-id="565ab-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="565ab-908">Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.</span><span class="sxs-lookup"><span data-stu-id="565ab-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="565ab-909">Une étendue est un type `IDisposable` qui est retourné par la méthode <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>. Elle s’applique tant qu’elle n’est pas supprimée.</span><span class="sxs-lookup"><span data-stu-id="565ab-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="565ab-910">Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :</span><span class="sxs-lookup"><span data-stu-id="565ab-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="565ab-911">Le code suivant active les étendues pour le fournisseur Console :</span><span class="sxs-lookup"><span data-stu-id="565ab-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="565ab-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="565ab-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="565ab-913">La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues.</span><span class="sxs-lookup"><span data-stu-id="565ab-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="565ab-914">Pour plus d'informations sur la configuration, reportez-vous à la section [Configuration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="565ab-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="565ab-915">Chaque message de journal fournit les informations incluses dans l’étendue :</span><span class="sxs-lookup"><span data-stu-id="565ab-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="565ab-916">Fournisseurs de journalisation intégrés</span><span class="sxs-lookup"><span data-stu-id="565ab-916">Built-in logging providers</span></span>

<span data-ttu-id="565ab-917">ASP.NET Core contient les fournisseurs suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="565ab-918">Console</span><span class="sxs-lookup"><span data-stu-id="565ab-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="565ab-919">Déboguer</span><span class="sxs-lookup"><span data-stu-id="565ab-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="565ab-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="565ab-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="565ab-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="565ab-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="565ab-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="565ab-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="565ab-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="565ab-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="565ab-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="565ab-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="565ab-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="565ab-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="565ab-926">Pour plus d’informations sur stdout et sur la journalisation du débogage avec le module ASP.NET Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="565ab-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="565ab-927">Fournisseur Console</span><span class="sxs-lookup"><span data-stu-id="565ab-927">Console provider</span></span>

<span data-ttu-id="565ab-928">Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console.</span><span class="sxs-lookup"><span data-stu-id="565ab-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="565ab-929">Pour voir la sortie de la journalisation Console, ouvrez une invite de commandes dans le dossier du projet et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="565ab-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="565ab-930">Fournisseur Debug</span><span class="sxs-lookup"><span data-stu-id="565ab-930">Debug provider</span></span>

<span data-ttu-id="565ab-931">Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (appels de la méthode `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="565ab-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="565ab-932">Sur Linux, ce fournisseur écrit la sortie de log dans */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="565ab-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="565ab-933">Fournisseur de source d’événements</span><span class="sxs-lookup"><span data-stu-id="565ab-933">Event Source provider</span></span>

<span data-ttu-id="565ab-934">Le package du fournisseur [Microsoft. extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) écrit dans une source d’événement multiplateforme portant le nom `Microsoft-Extensions-Logging` .</span><span class="sxs-lookup"><span data-stu-id="565ab-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="565ab-935">Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="565ab-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="565ab-936">Le fournisseur de la source d’événements est ajouté automatiquement lorsque `CreateDefaultBuilder` est appelé pour générer l’hôte.</span><span class="sxs-lookup"><span data-stu-id="565ab-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="565ab-937">Utilisez l' [utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux.</span><span class="sxs-lookup"><span data-stu-id="565ab-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="565ab-938">Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="565ab-939">Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**.</span><span class="sxs-lookup"><span data-stu-id="565ab-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="565ab-940">(N’oubliez pas d’inclure l’astérisque au début de la chaîne.)</span><span class="sxs-lookup"><span data-stu-id="565ab-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="565ab-942">Fournisseur EventLog Windows</span><span class="sxs-lookup"><span data-stu-id="565ab-942">Windows EventLog provider</span></span>

<span data-ttu-id="565ab-943">Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="565ab-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="565ab-944">Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) vous permettent de passer à <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="565ab-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="565ab-945">Si `null` ou n’est pas spécifié, les paramètres par défaut suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="565ab-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="565ab-946">`LogName`: « Application »</span><span class="sxs-lookup"><span data-stu-id="565ab-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="565ab-947">`SourceName`: « .NET Runtime »</span><span class="sxs-lookup"><span data-stu-id="565ab-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="565ab-948">`MachineName`: Le nom de l’ordinateur local est utilisé.</span><span class="sxs-lookup"><span data-stu-id="565ab-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="565ab-949">Les événements sont consignés pour le [niveau d’avertissement et supérieur](#log-level).</span><span class="sxs-lookup"><span data-stu-id="565ab-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="565ab-950">L’exemple suivant définit le niveau de journalisation par défaut du journal des événements sur <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="565ab-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="565ab-951">Fournisseur TraceSource</span><span class="sxs-lookup"><span data-stu-id="565ab-951">TraceSource provider</span></span>

<span data-ttu-id="565ab-952">Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="565ab-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="565ab-953">Les [surcharges AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) vous permettent de passer un commutateur de source et un écouteur de suivi.</span><span class="sxs-lookup"><span data-stu-id="565ab-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="565ab-954">Pour pouvoir utiliser ce fournisseur, il faut que l’application s’exécute sur .NET Framework (au lieu de .NET Core).</span><span class="sxs-lookup"><span data-stu-id="565ab-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="565ab-955">Le fournisseur peut acheminer les messages vers différents [détecteurs d’événements](/dotnet/framework/debug-trace-profile/trace-listeners), comme <xref:System.Diagnostics.TextWriterTraceListener> (utilisé dans l’exemple d’application).</span><span class="sxs-lookup"><span data-stu-id="565ab-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="565ab-956">Fournisseur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="565ab-956">Azure App Service provider</span></span>

<span data-ttu-id="565ab-957">Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.</span><span class="sxs-lookup"><span data-stu-id="565ab-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="565ab-958">Le package du fournisseur n’est pas inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="565ab-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="565ab-959">Lorsque vous ciblez .NET framework ou référencez le métapackage `Microsoft.AspNetCore.App`, ajoutez le package de fournisseur dans le projet.</span><span class="sxs-lookup"><span data-stu-id="565ab-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="565ab-960">Une surcharge <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permet de transmettre <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="565ab-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="565ab-961">L’objet de paramètres peut remplacer les paramètres par défaut, comme le modèle de sortie de journalisation, le nom d’objet blob et la limite de taille de fichier.</span><span class="sxs-lookup"><span data-stu-id="565ab-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="565ab-962">(*Modèle de sortie* est un modèle de message qui s’applique à tous les journaux en plus de ce qui est fourni avec un appel de méthode `ILogger`.)</span><span class="sxs-lookup"><span data-stu-id="565ab-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="565ab-963">En cas de déploiement sur une application App Service, celle-ci applique les paramètres définis dans la section [Journaux App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="565ab-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="565ab-964">Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="565ab-965">**Journalisation des applications (système de fichiers)**</span><span class="sxs-lookup"><span data-stu-id="565ab-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="565ab-966">**Journalisation des applications (objet blob)**</span><span class="sxs-lookup"><span data-stu-id="565ab-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="565ab-967">L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*.</span><span class="sxs-lookup"><span data-stu-id="565ab-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="565ab-968">La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2.</span><span class="sxs-lookup"><span data-stu-id="565ab-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="565ab-969">Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="565ab-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="565ab-970">Le fournisseur fonctionne uniquement quand le projet s’exécute dans l’environnement Azure.</span><span class="sxs-lookup"><span data-stu-id="565ab-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="565ab-971">Il n’a aucun effet si le projet s’exécute localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.</span><span class="sxs-lookup"><span data-stu-id="565ab-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="565ab-972">Streaming des journaux Azure</span><span class="sxs-lookup"><span data-stu-id="565ab-972">Azure log streaming</span></span>

<span data-ttu-id="565ab-973">La diffusion en continu des journaux Azure permet d’afficher l’activité de journalisation en temps réel aux emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="565ab-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="565ab-974">Serveur d'applications</span><span class="sxs-lookup"><span data-stu-id="565ab-974">The app server</span></span>
* <span data-ttu-id="565ab-975">Serveur web</span><span class="sxs-lookup"><span data-stu-id="565ab-975">The web server</span></span>
* <span data-ttu-id="565ab-976">Suivi des demandes ayant échoué</span><span class="sxs-lookup"><span data-stu-id="565ab-976">Failed request tracing</span></span>

<span data-ttu-id="565ab-977">Pour configurer le streaming des journaux Azure :</span><span class="sxs-lookup"><span data-stu-id="565ab-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="565ab-978">Accédez à la page **Journaux App Service** dans le portail de votre application.</span><span class="sxs-lookup"><span data-stu-id="565ab-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="565ab-979">Définissez **Journal des applications (Système de fichiers)** sur **Activé**.</span><span class="sxs-lookup"><span data-stu-id="565ab-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="565ab-980">Choisissez le **niveau** du journal.</span><span class="sxs-lookup"><span data-stu-id="565ab-980">Choose the log **Level**.</span></span> <span data-ttu-id="565ab-981">Ce paramètre s’applique uniquement à la diffusion en continu de journaux Azure, pas aux autres fournisseurs de journalisation de l’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="565ab-982">Accédez à la page **Streaming des journaux** pour voir les messages d’application.</span><span class="sxs-lookup"><span data-stu-id="565ab-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="565ab-983">Ils sont consignés par application par le biais de l’interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="565ab-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="565ab-984">Journalisation des traces Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="565ab-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="565ab-985">Le package de fournisseur [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit des journaux dans Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="565ab-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="565ab-986">Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie.</span><span class="sxs-lookup"><span data-stu-id="565ab-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="565ab-987">Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.</span><span class="sxs-lookup"><span data-stu-id="565ab-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="565ab-988">Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="565ab-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="565ab-989">Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="565ab-990">N’utilisez pas le [package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;destiné à ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="565ab-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="565ab-991">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="565ab-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="565ab-992">Vue d’ensemble d’Application Insights</span><span class="sxs-lookup"><span data-stu-id="565ab-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="565ab-993">[Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="565ab-994">[Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.</span><span class="sxs-lookup"><span data-stu-id="565ab-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="565ab-995">[Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="565ab-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="565ab-996">[Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="565ab-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="565ab-997">Fournisseurs de journalisation tiers</span><span class="sxs-lookup"><span data-stu-id="565ab-997">Third-party logging providers</span></span>

<span data-ttu-id="565ab-998">Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="565ab-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="565ab-999">[elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="565ab-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="565ab-1001">[JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="565ab-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="565ab-1002">[KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="565ab-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="565ab-1003">[Log4net](https://logging.apache.org/log4net/) ([GitHub référentiel](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="565ab-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="565ab-1004">[Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="565ab-1005">[NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="565ab-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="565ab-1006">[Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="565ab-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="565ab-1007">[Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="565ab-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="565ab-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="565ab-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="565ab-1009">Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="565ab-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="565ab-1010">L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :</span><span class="sxs-lookup"><span data-stu-id="565ab-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="565ab-1011">Ajoutez un package NuGet à votre projet.</span><span class="sxs-lookup"><span data-stu-id="565ab-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="565ab-1012">Appeler une `ILoggerFactory` méthode d’extension fournie par le Framework de journalisation.</span><span class="sxs-lookup"><span data-stu-id="565ab-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="565ab-1013">Pour plus d’informations, consultez la documentation de chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="565ab-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="565ab-1014">Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="565ab-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="565ab-1015">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="565ab-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
