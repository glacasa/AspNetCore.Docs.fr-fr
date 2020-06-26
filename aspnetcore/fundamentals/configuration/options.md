---
title: Modèle d’options dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le modèle d’options pour représenter des groupes de paramètres associés dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: 300b26c198e6ea07fe83af8fb9ae967e814396fb
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408355"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="5c504-103">Modèle d’options dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c504-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c504-104">Par [Kirk Larkin](https://twitter.com/serpent5) et [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="5c504-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="5c504-105">Le modèle d’options utilise des classes pour fournir un accès fortement typé aux groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="5c504-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="5c504-106">Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :</span><span class="sxs-lookup"><span data-stu-id="5c504-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="5c504-107">Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.</span><span class="sxs-lookup"><span data-stu-id="5c504-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="5c504-108">[Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="5c504-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="5c504-109">Ces options fournissent également un mécanisme de validation des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="5c504-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="5c504-110">Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="5c504-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="5c504-111">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c504-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="5c504-112">Liaison de la configuration hiérarchique</span><span class="sxs-lookup"><span data-stu-id="5c504-112">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="5c504-113">Interfaces d’options</span><span class="sxs-lookup"><span data-stu-id="5c504-113">Options interfaces</span></span>

<span data-ttu-id="5c504-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="5c504-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="5c504-115">Ne prend ***pas*** en charge :</span><span class="sxs-lookup"><span data-stu-id="5c504-115">Does ***not*** support:</span></span>
  * <span data-ttu-id="5c504-116">Lecture des données de configuration après le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-116">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="5c504-117">Options nommées</span><span class="sxs-lookup"><span data-stu-id="5c504-117">Named options</span></span>](#named)
* <span data-ttu-id="5c504-118">Est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#singleton) et peut être injecté dans n’importe quelle [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="5c504-118">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="5c504-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="5c504-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="5c504-120">Est utile dans les scénarios où les options doivent être recalculées à chaque demande.</span><span class="sxs-lookup"><span data-stu-id="5c504-120">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="5c504-121">Pour plus d’informations, consultez [utiliser IOptionsSnapshot pour lire des données mises à jour](#ios).</span><span class="sxs-lookup"><span data-stu-id="5c504-121">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="5c504-122">Est inscrit en tant qu' [étendue](xref:fundamentals/dependency-injection#scoped) et ne peut donc pas être injecté dans un service Singleton.</span><span class="sxs-lookup"><span data-stu-id="5c504-122">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="5c504-123">Prend en charge les [options nommées](#named)</span><span class="sxs-lookup"><span data-stu-id="5c504-123">Supports [named options](#named)</span></span>

<span data-ttu-id="5c504-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="5c504-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="5c504-125">Est utilisé pour récupérer des options et gérer les notifications d’options pour les `TOptions` instances.</span><span class="sxs-lookup"><span data-stu-id="5c504-125">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="5c504-126">Est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#singleton) et peut être injecté dans n’importe quelle [durée de vie de service](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="5c504-126">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="5c504-127">Prend en charge :</span><span class="sxs-lookup"><span data-stu-id="5c504-127">Supports:</span></span>
  * <span data-ttu-id="5c504-128">Notifications de modifications</span><span class="sxs-lookup"><span data-stu-id="5c504-128">Change notifications</span></span>
  * [<span data-ttu-id="5c504-129">Options nommées</span><span class="sxs-lookup"><span data-stu-id="5c504-129">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="5c504-130">Configuration rechargeable</span><span class="sxs-lookup"><span data-stu-id="5c504-130">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="5c504-131">Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="5c504-131">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="5c504-132">Les scénarios [postérieurs à la configuration](#options-post-configuration) permettent de définir ou de modifier des options après l’exécution de toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configurations.</span><span class="sxs-lookup"><span data-stu-id="5c504-132">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="5c504-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options.</span><span class="sxs-lookup"><span data-stu-id="5c504-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="5c504-134">Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-134">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="5c504-135">L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations.</span><span class="sxs-lookup"><span data-stu-id="5c504-135">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="5c504-136">Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.</span><span class="sxs-lookup"><span data-stu-id="5c504-136">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="5c504-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="5c504-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="5c504-138">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="5c504-139">Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-139">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="5c504-140">La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.</span><span class="sxs-lookup"><span data-stu-id="5c504-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="5c504-141">Utiliser IOptionsSnapshot pour lire les données mises à jour</span><span class="sxs-lookup"><span data-stu-id="5c504-141">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="5c504-142">À l’aide de <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> , les options sont calculées une fois par demande, en cas d’accès et de mise en cache pour la durée de vie de la demande.</span><span class="sxs-lookup"><span data-stu-id="5c504-142">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="5c504-143">Les modifications apportées à la configuration sont lues après le démarrage de l’application lors de l’utilisation de fournisseurs de configuration qui prennent en charge la lecture des valeurs de configuration mises</span><span class="sxs-lookup"><span data-stu-id="5c504-143">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="5c504-144">La différence entre `IOptionsMonitor` et `IOptionsSnapshot` est que :</span><span class="sxs-lookup"><span data-stu-id="5c504-144">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="5c504-145">`IOptionsMonitor`est un [service Singleton](xref:fundamentals/dependency-injection#singleton) qui récupère les valeurs d’option actuelles à tout moment, ce qui est particulièrement utile dans les dépendances Singleton.</span><span class="sxs-lookup"><span data-stu-id="5c504-145">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="5c504-146">`IOptionsSnapshot`est un [service étendu](xref:fundamentals/dependency-injection#scoped) et fournit un instantané des options au moment de la construction de l' `IOptionsSnapshot<T>` objet.</span><span class="sxs-lookup"><span data-stu-id="5c504-146">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="5c504-147">Les instantanés d’options sont conçus pour être utilisés avec des dépendances transitoires et délimitées.</span><span class="sxs-lookup"><span data-stu-id="5c504-147">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="5c504-148">Le code suivant utilise <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="5c504-148">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="5c504-149">Le code suivant inscrit une instance de configuration qui se `MyOptions` lie à :</span><span class="sxs-lookup"><span data-stu-id="5c504-149">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="5c504-150">Dans le code précédent, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.</span><span class="sxs-lookup"><span data-stu-id="5c504-150">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="5c504-151">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="5c504-151">IOptionsMonitor</span></span>

<span data-ttu-id="5c504-152">Le code suivant inscrit une instance de configuration qui est `MyOptions` liée à.</span><span class="sxs-lookup"><span data-stu-id="5c504-152">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="5c504-153">L'exemple suivant utilise <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="5c504-153">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="5c504-154">Dans le code précédent, par défaut, les modifications apportées au fichier de configuration JSON après le démarrage de l’application sont lues.</span><span class="sxs-lookup"><span data-stu-id="5c504-154">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="5c504-155">Prise en charge des options nommées à l’aide de IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="5c504-155">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="5c504-156">Options nommées :</span><span class="sxs-lookup"><span data-stu-id="5c504-156">Named options:</span></span>

* <span data-ttu-id="5c504-157">Sont utiles lorsque plusieurs sections de configuration sont liées aux mêmes propriétés.</span><span class="sxs-lookup"><span data-stu-id="5c504-157">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="5c504-158">Respecte la casse.</span><span class="sxs-lookup"><span data-stu-id="5c504-158">Are case sensitive.</span></span>

<span data-ttu-id="5c504-159">Prenez en compte les *appsettings.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="5c504-159">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="5c504-160">Au lieu de créer deux classes pour lier `TopItem:Month` et `TopItem:Year` , la classe suivante est utilisée pour chaque section :</span><span class="sxs-lookup"><span data-stu-id="5c504-160">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="5c504-161">Le code suivant configure les options nommées :</span><span class="sxs-lookup"><span data-stu-id="5c504-161">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="5c504-162">Le code suivant affiche les options nommées :</span><span class="sxs-lookup"><span data-stu-id="5c504-162">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="5c504-163">Toutes les options sont des instances nommées.</span><span class="sxs-lookup"><span data-stu-id="5c504-163">All options are named instances.</span></span> <span data-ttu-id="5c504-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>les instances sont traitées comme ciblant l' `Options.DefaultName` instance, qui est `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="5c504-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="5c504-165">En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="5c504-166">L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="5c504-166">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="5c504-167">L' `null` option nommée est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique.</span><span class="sxs-lookup"><span data-stu-id="5c504-167">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="5c504-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette Convention.</span><span class="sxs-lookup"><span data-stu-id="5c504-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="5c504-169">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="5c504-169">OptionsBuilder API</span></span>

<span data-ttu-id="5c504-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="5c504-171">`OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="5c504-171">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="5c504-172">La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5c504-172">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="5c504-173">`OptionsBuilder`est utilisé dans la section des [options de validation](#val) .</span><span class="sxs-lookup"><span data-stu-id="5c504-173">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="5c504-174">Utiliser les services d’injection de dépendances (DI) pour configurer des options</span><span class="sxs-lookup"><span data-stu-id="5c504-174">Use DI services to configure options</span></span>

<span data-ttu-id="5c504-175">Les services sont accessibles à partir de l’injection de dépendances lors de la configuration des options de deux manières :</span><span class="sxs-lookup"><span data-stu-id="5c504-175">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="5c504-176">Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="5c504-176">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="5c504-177">`OptionsBuilder<TOptions>`fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui autorisent l’utilisation de cinq services au maximum pour configurer des options :</span><span class="sxs-lookup"><span data-stu-id="5c504-177">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="5c504-178">Créez un type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.</span><span class="sxs-lookup"><span data-stu-id="5c504-178">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="5c504-179">Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service.</span><span class="sxs-lookup"><span data-stu-id="5c504-179">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="5c504-180">La création d’un type équivaut à ce que fait l’infrastructure lors de l’appel de [configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="5c504-180">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="5c504-181">L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés.</span><span class="sxs-lookup"><span data-stu-id="5c504-181">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="5c504-182">Validation des options</span><span class="sxs-lookup"><span data-stu-id="5c504-182">Options validation</span></span>

<span data-ttu-id="5c504-183">La validation des options permet de valider les valeurs d’option.</span><span class="sxs-lookup"><span data-stu-id="5c504-183">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="5c504-184">Prenez en compte les *appsettings.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="5c504-184">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="5c504-185">La classe suivante crée une liaison avec la `"MyConfig"` section de configuration et applique deux `DataAnnotations` règles :</span><span class="sxs-lookup"><span data-stu-id="5c504-185">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="5c504-186">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="5c504-186">The following code:</span></span>

* <span data-ttu-id="5c504-187">Appelle <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> pour recevoir un [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) qui crée une liaison avec la `MyConfigOptions` classe.</span><span class="sxs-lookup"><span data-stu-id="5c504-187">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="5c504-188">Appelle <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> pour activer la validation à l’aide de `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="5c504-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="5c504-189">La `ValidateDataAnnotations` méthode d’extension est définie dans le package NuGet [Microsoft. extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="5c504-189">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="5c504-190">Pour les applications Web qui utilisent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK), ce package est référencé implicitement à partir de l’infrastructure partagée.</span><span class="sxs-lookup"><span data-stu-id="5c504-190">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="5c504-191">Le code suivant affiche les valeurs de configuration ou les erreurs de validation :</span><span class="sxs-lookup"><span data-stu-id="5c504-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="5c504-192">Le code suivant applique une règle de validation plus complexe à l’aide d’un délégué :</span><span class="sxs-lookup"><span data-stu-id="5c504-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="5c504-193">IValidateOptions pour la validation complexe</span><span class="sxs-lookup"><span data-stu-id="5c504-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="5c504-194">La classe suivante implémente <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="5c504-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="5c504-195">`IValidateOptions`permet de déplacer le code de validation de `StartUp` et vers une classe.</span><span class="sxs-lookup"><span data-stu-id="5c504-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="5c504-196">À l’aide du code précédent, la validation est activée dans `Startup.ConfigureServices` avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="5c504-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="5c504-197">Options de post-configuration</span><span class="sxs-lookup"><span data-stu-id="5c504-197">Options post-configuration</span></span>

<span data-ttu-id="5c504-198">Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="5c504-199">La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="5c504-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="5c504-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :</span><span class="sxs-lookup"><span data-stu-id="5c504-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="5c504-201">Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :</span><span class="sxs-lookup"><span data-stu-id="5c504-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="5c504-202">Accès aux options au démarrage</span><span class="sxs-lookup"><span data-stu-id="5c504-202">Accessing options during startup</span></span>

<span data-ttu-id="5c504-203"><xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.</span><span class="sxs-lookup"><span data-stu-id="5c504-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="5c504-204">N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5c504-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c504-205">Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="5c504-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="5c504-206">Package NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="5c504-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="5c504-207">Le package [urationExtensionsMicrosoft.Extensions.Options.Config](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) est implicitement référencé dans les applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="5c504-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5c504-208">Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="5c504-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="5c504-209">Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :</span><span class="sxs-lookup"><span data-stu-id="5c504-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="5c504-210">Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.</span><span class="sxs-lookup"><span data-stu-id="5c504-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="5c504-211">[Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="5c504-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="5c504-212">Ces options fournissent également un mécanisme de validation des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="5c504-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="5c504-213">Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="5c504-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="5c504-214">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c504-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5c504-215">Prérequis</span><span class="sxs-lookup"><span data-stu-id="5c504-215">Prerequisites</span></span>

<span data-ttu-id="5c504-216">Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="5c504-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="5c504-217">Interfaces d’options</span><span class="sxs-lookup"><span data-stu-id="5c504-217">Options interfaces</span></span>

<span data-ttu-id="5c504-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> permet de récupérer des options et de gérer les notifications d’options pour les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="5c504-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="5c504-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="5c504-220">Notifications de modifications</span><span class="sxs-lookup"><span data-stu-id="5c504-220">Change notifications</span></span>
* [<span data-ttu-id="5c504-221">Options nommées</span><span class="sxs-lookup"><span data-stu-id="5c504-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="5c504-222">Configuration rechargeable</span><span class="sxs-lookup"><span data-stu-id="5c504-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="5c504-223">Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="5c504-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="5c504-224">Des scénarios [post-configuration](#options-post-configuration) vous permettent de définir ou de modifier les options après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="5c504-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options.</span><span class="sxs-lookup"><span data-stu-id="5c504-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="5c504-226">Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="5c504-227">L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations.</span><span class="sxs-lookup"><span data-stu-id="5c504-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="5c504-228">Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.</span><span class="sxs-lookup"><span data-stu-id="5c504-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="5c504-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="5c504-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="5c504-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="5c504-231">Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="5c504-232">La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.</span><span class="sxs-lookup"><span data-stu-id="5c504-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="5c504-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est utile dans les scénarios où des options doivent être recalculées à chaque requête.</span><span class="sxs-lookup"><span data-stu-id="5c504-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="5c504-234">Pour plus d’informations, consultez la section [Recharger les données de configuration avec IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="5c504-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="5c504-235"><xref:Microsoft.Extensions.Options.IOptions%601> peut être utilisée pour prendre en charge des options.</span><span class="sxs-lookup"><span data-stu-id="5c504-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="5c504-236">Toutefois, <xref:Microsoft.Extensions.Options.IOptions%601> ne prend pas en charge les scénarios <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> précédents.</span><span class="sxs-lookup"><span data-stu-id="5c504-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="5c504-237">Vous pouvez continuer à utiliser <xref:Microsoft.Extensions.Options.IOptions%601> dans des infrastructures et bibliothèques existantes qui utilisent déjà l’interface <xref:Microsoft.Extensions.Options.IOptions%601> mais ne nécessitent pas les scénarios fournis par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="5c504-238">Configuration des options générales</span><span class="sxs-lookup"><span data-stu-id="5c504-238">General options configuration</span></span>

<span data-ttu-id="5c504-239">La configuration des options générales est illustrée dans l’exemple 1 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="5c504-240">Une classe d’options doit être non abstraite avec un constructeur public sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="5c504-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="5c504-241">La classe suivante, `MyOptions`, a deux propriétés : `Option1` et `Option2`.</span><span class="sxs-lookup"><span data-stu-id="5c504-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="5c504-242">Définir des valeurs par défaut est facultatif, mais le constructeur de classe dans l’exemple suivant définit la valeur par défaut `Option1`.</span><span class="sxs-lookup"><span data-stu-id="5c504-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="5c504-243">`Option2` a une valeur par défaut définie par l’initialisation directe de la propriété (*Models/MyOptions.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="5c504-244">La classe `MyOptions` est ajoutée au conteneur de service avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et liée à la configuration :</span><span class="sxs-lookup"><span data-stu-id="5c504-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="5c504-245">Le modèle de page suivant utilise une [injection de dépendances de constructeur](xref:mvc/controllers/dependency-injection) avec <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour accéder aux paramètres (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="5c504-246">Le fichier *appsettings.json* de l’exemple spécifie des valeurs pour `option1` et `option2` :</span><span class="sxs-lookup"><span data-stu-id="5c504-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="5c504-247">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="5c504-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="5c504-248">Quand vous utilisez un <xref:System.Configuration.ConfigurationBuilder> personnalisé pour charger une configuration d’options à partir d’un fichier de paramètres, vérifiez que le chemin de base est correctement défini :</span><span class="sxs-lookup"><span data-stu-id="5c504-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="5c504-249">Vous n’avez pas besoin de définir explicitement le chemin de base quand vous chargez une configuration d’options à partir du fichier de paramètres via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="5c504-250">Configurer des options simples avec un délégué</span><span class="sxs-lookup"><span data-stu-id="5c504-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="5c504-251">La configuration d’options simples avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="5c504-252">Utilisez un délégué pour définir les valeurs des options.</span><span class="sxs-lookup"><span data-stu-id="5c504-252">Use a delegate to set options values.</span></span> <span data-ttu-id="5c504-253">L’exemple d’application utilise la classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="5c504-254">Dans le code suivant, un second service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="5c504-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="5c504-255">Il utilise un délégué pour configurer la liaison avec `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="5c504-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="5c504-256">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c504-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="5c504-257">Vous pouvez ajouter plusieurs fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="5c504-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="5c504-258">Des fournisseurs de configuration sont disponibles à partir de packages NuGet et sont appliqués dans l’ordre de leur inscription.</span><span class="sxs-lookup"><span data-stu-id="5c504-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="5c504-259">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5c504-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="5c504-260">Chaque appel à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> ajoute un service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="5c504-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="5c504-261">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont toutes deux spécifiées dans *appsettings.json*, mais les valeurs de `Option1` et `Option2` sont remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="5c504-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="5c504-262">Quand plusieurs services de configuration sont activés, la dernière source de configuration spécifiée *gagne* et définit la valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="5c504-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="5c504-263">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="5c504-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="5c504-264">Configuration des sous-options</span><span class="sxs-lookup"><span data-stu-id="5c504-264">Suboptions configuration</span></span>

<span data-ttu-id="5c504-265">La configuration des sous-options est illustrée dans l’exemple 3 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="5c504-266">Les applications doivent créer des classes d’options qui appartiennent à des groupes de scénarios spécifiques (classes) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="5c504-267">Les parties de l’application qui requièrent des valeurs de configuration ne doivent avoir accès qu’aux valeurs de configuration qu’elles utilisent.</span><span class="sxs-lookup"><span data-stu-id="5c504-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="5c504-268">Au moment de la liaison des options à la configuration, chaque propriété du type options est liée à une clé de configuration sous la forme `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="5c504-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="5c504-269">Par exemple, la propriété `MyOptions.Option1` est liée à la clé `Option1`, qui est lue à partir de la propriété `option1` dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5c504-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="5c504-270">Dans le code suivant, un troisième service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="5c504-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="5c504-271">Il lie `MySubOptions` à la section `subsection` du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="5c504-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="5c504-272">La `GetSection` méthode requiert l' <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> espace de noms.</span><span class="sxs-lookup"><span data-stu-id="5c504-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="5c504-273">Le fichier *appsettings.json* de l’exemple définit un membre `subsection` avec des clés pour `suboption1` et `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="5c504-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="5c504-274">La classe `MySubOptions` définit des propriétés, `SubOption1` et `SubOption2`, pour conserver les valeurs des options (*Models/MySubOptions.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="5c504-275">La méthode `OnGet` du modèle de page retourne une chaîne avec les valeurs des options (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="5c504-276">Quand l’application est exécutée, la méthode `OnGet` retourne une chaîne indiquant les valeurs de la classe de sous-options :</span><span class="sxs-lookup"><span data-stu-id="5c504-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="5c504-277">Injection d’options</span><span class="sxs-lookup"><span data-stu-id="5c504-277">Options injection</span></span>

<span data-ttu-id="5c504-278">L’injection d’options est illustrée dans l’exemple 4 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="5c504-279">Injecter <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans :</span><span class="sxs-lookup"><span data-stu-id="5c504-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="5c504-280">Une Razor page ou une vue MVC avec la [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span><span class="sxs-lookup"><span data-stu-id="5c504-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="5c504-281">Modèle de page ou de vue.</span><span class="sxs-lookup"><span data-stu-id="5c504-281">A page or view model.</span></span>

<span data-ttu-id="5c504-282">L’exemple suivant tiré de l’exemple d’application injecte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans un modèle de page (*pages/index. cshtml. cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="5c504-283">L’exemple d’application montre comment injecter `IOptionsMonitor<MyOptions>` avec une directive `@inject` :</span><span class="sxs-lookup"><span data-stu-id="5c504-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="5c504-284">Quand l’application est exécutée, les valeurs d’options sont affichées dans la page rendue :</span><span class="sxs-lookup"><span data-stu-id="5c504-284">When the app is run, the options values are shown in the rendered page:</span></span>

![Les valeurs d’options Option1: value1_from_json et Option2: -1 sont chargées à partir du modèle et par injection dans la vue.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="5c504-286">Recharger les données de configuration avec IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="5c504-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="5c504-287">Le rechargement des données de configuration avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est illustré dans l’exemple 5 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="5c504-288">À l’aide de <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> , les options sont calculées une fois par demande, en cas d’accès et de mise en cache pour la durée de vie de la demande.</span><span class="sxs-lookup"><span data-stu-id="5c504-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="5c504-289">La différence entre `IOptionsMonitor` et `IOptionsSnapshot` est que :</span><span class="sxs-lookup"><span data-stu-id="5c504-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="5c504-290">`IOptionsMonitor`est un [service Singleton](xref:fundamentals/dependency-injection#singleton) qui récupère les valeurs d’option actuelles à tout moment, ce qui est particulièrement utile dans les dépendances Singleton.</span><span class="sxs-lookup"><span data-stu-id="5c504-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="5c504-291">`IOptionsSnapshot`est un [service étendu](xref:fundamentals/dependency-injection#scoped) et fournit un instantané des options au moment de la construction de l' `IOptionsSnapshot<T>` objet.</span><span class="sxs-lookup"><span data-stu-id="5c504-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="5c504-292">Les instantanés d’options sont conçus pour être utilisés avec des dépendances transitoires et délimitées.</span><span class="sxs-lookup"><span data-stu-id="5c504-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="5c504-293">Dans l’exemple suivant, un <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est créé à la suite de la modification du fichier *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="5c504-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="5c504-294">Plusieurs demandes adressées au serveur retournent des valeurs constantes fournies par le fichier *appsettings.json* tant que ce dernier n’est pas changé et que la configuration n’est pas rechargée.</span><span class="sxs-lookup"><span data-stu-id="5c504-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="5c504-295">L’image suivante montre les valeurs `option1` et `option2` initiales chargées à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="5c504-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="5c504-296">Changez les valeurs dans le fichier *appsettings.json* en `value1_from_json UPDATED` et `200`.</span><span class="sxs-lookup"><span data-stu-id="5c504-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="5c504-297">Enregistrez le *appsettings.jsdans* le fichier.</span><span class="sxs-lookup"><span data-stu-id="5c504-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="5c504-298">Actualisez le navigateur pour constater la mise à jour des valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="5c504-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="5c504-299">Prise en charge des options nommées avec IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="5c504-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="5c504-300">La prise en charge des options nommées avec <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> est illustrée dans l’exemple 6 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="5c504-301">La prise en charge des options nommées permet à l’application de faire la distinction entre les configurations d’options nommées.</span><span class="sxs-lookup"><span data-stu-id="5c504-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="5c504-302">Dans l’exemple d’application, les options nommées sont déclarées avec [OptionsServiceCollectionExtensions.Configurer](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), qui appelle [ConfigureNamedOptions \<TOptions> . Configurez](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="5c504-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="5c504-303">Les options nommées respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="5c504-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="5c504-304">L’exemple d’application accède aux options nommées avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="5c504-305">Quand l’exemple d’application est exécuté, les options nommées sont retournées :</span><span class="sxs-lookup"><span data-stu-id="5c504-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="5c504-306">Les valeurs `named_options_1`, issues de la configuration, sont chargées à partir du fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5c504-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="5c504-307">Les valeurs `named_options_2` sont fournies par :</span><span class="sxs-lookup"><span data-stu-id="5c504-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="5c504-308">Le délégué `named_options_2` dans `ConfigureServices` pour `Option1`.</span><span class="sxs-lookup"><span data-stu-id="5c504-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="5c504-309">La valeur par défaut `Option2` fournie par la classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="5c504-310">Configurer toutes les options avec la méthode ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="5c504-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="5c504-311">Configurez toutes les instances d’options avec la méthode <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="5c504-312">Le code suivant configure `Option1` pour toutes les instances de configuration ayant une valeur commune.</span><span class="sxs-lookup"><span data-stu-id="5c504-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="5c504-313">Ajoutez le code suivant manuellement à la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5c504-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="5c504-314">Exécuter l’exemple d’application après avoir ajouté le code produit le résultat suivant :</span><span class="sxs-lookup"><span data-stu-id="5c504-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="5c504-315">Toutes les options sont des instances nommées.</span><span class="sxs-lookup"><span data-stu-id="5c504-315">All options are named instances.</span></span> <span data-ttu-id="5c504-316">Les instances <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existantes sont traitées comme ciblant l’instance `Options.DefaultName`, qui est `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="5c504-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="5c504-317">En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="5c504-318">L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="5c504-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="5c504-319">L’option nommée `null` est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette convention).</span><span class="sxs-lookup"><span data-stu-id="5c504-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="5c504-320">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="5c504-320">OptionsBuilder API</span></span>

<span data-ttu-id="5c504-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="5c504-322">`OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="5c504-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="5c504-323">La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5c504-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="5c504-324">Utiliser les services d’injection de dépendances (DI) pour configurer des options</span><span class="sxs-lookup"><span data-stu-id="5c504-324">Use DI services to configure options</span></span>

<span data-ttu-id="5c504-325">Vous pouvez accéder à d’autres services à partir de l’injection de dépendances pendant que vous configurez des options de deux manières différentes :</span><span class="sxs-lookup"><span data-stu-id="5c504-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="5c504-326">Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="5c504-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="5c504-327">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui vous permettent d’utiliser jusqu’à cinq services pour configurer des options :</span><span class="sxs-lookup"><span data-stu-id="5c504-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="5c504-328">Créez votre propre type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.</span><span class="sxs-lookup"><span data-stu-id="5c504-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="5c504-329">Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service.</span><span class="sxs-lookup"><span data-stu-id="5c504-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="5c504-330">Créer votre propre type équivaut à ce que fait automatiquement le framework quand vous utilisez [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="5c504-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="5c504-331">L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés.</span><span class="sxs-lookup"><span data-stu-id="5c504-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="5c504-332">Validation des options</span><span class="sxs-lookup"><span data-stu-id="5c504-332">Options validation</span></span>

<span data-ttu-id="5c504-333">La validation des options vous permet de valider les options lorsque celles-ci sont configurées.</span><span class="sxs-lookup"><span data-stu-id="5c504-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="5c504-334">Appelez `Validate` avec une méthode de validation qui retourne `true` si les options sont valides et `false` si elles ne sont pas valides :</span><span class="sxs-lookup"><span data-stu-id="5c504-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="5c504-335">L’exemple précédent définit l’instance d’options nommée sur `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="5c504-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="5c504-336">L'instance d’options par défaut est `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="5c504-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="5c504-337">La validation s’exécute lorsque l’instance d’options est créée.</span><span class="sxs-lookup"><span data-stu-id="5c504-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="5c504-338">La validation d’une instance options est garantie lors de sa première accès.</span><span class="sxs-lookup"><span data-stu-id="5c504-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5c504-339">La validation des options ne protège pas contre les modifications apportées aux options une fois l’instance des options créée.</span><span class="sxs-lookup"><span data-stu-id="5c504-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="5c504-340">Par exemple, `IOptionsSnapshot` les options sont créées et validées une fois par demande lorsque les options y accèdent pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="5c504-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="5c504-341">Les `IOptionsSnapshot` options ne sont pas validées à nouveau lors des tentatives d’accès suivantes *pour la même requête*.</span><span class="sxs-lookup"><span data-stu-id="5c504-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="5c504-342">La méthode `Validate` accepte un `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="5c504-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="5c504-343">Pour personnaliser entièrement la validation, implémentez `IValidateOptions<TOptions>`, ce qui permet :</span><span class="sxs-lookup"><span data-stu-id="5c504-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="5c504-344">Validation de plusieurs types d’options : `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="5c504-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="5c504-345">Validation qui dépend d’un autre type d’option : `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="5c504-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="5c504-346">`IValidateOptions` valide :</span><span class="sxs-lookup"><span data-stu-id="5c504-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="5c504-347">Une instance d’options nommée spécifique.</span><span class="sxs-lookup"><span data-stu-id="5c504-347">A specific named options instance.</span></span>
* <span data-ttu-id="5c504-348">Toutes les options quand `name` est `null`.</span><span class="sxs-lookup"><span data-stu-id="5c504-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="5c504-349">Retourne `ValidateOptionsResult` à partir de votre implémentation de l’interface :</span><span class="sxs-lookup"><span data-stu-id="5c504-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="5c504-350">La validation basée sur les annotations de données est disponible à partir du package [Microsoft. extensions. options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) en appelant la <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> méthode sur `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="5c504-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="5c504-351">`Microsoft.Extensions.Options.DataAnnotations`est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5c504-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="5c504-352">La validation hâtive (échec rapide au démarrage) est à l’étude pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="5c504-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="5c504-353">Options de post-configuration</span><span class="sxs-lookup"><span data-stu-id="5c504-353">Options post-configuration</span></span>

<span data-ttu-id="5c504-354">Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="5c504-355">La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="5c504-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="5c504-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :</span><span class="sxs-lookup"><span data-stu-id="5c504-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="5c504-357">Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :</span><span class="sxs-lookup"><span data-stu-id="5c504-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="5c504-358">Accès aux options au démarrage</span><span class="sxs-lookup"><span data-stu-id="5c504-358">Accessing options during startup</span></span>

<span data-ttu-id="5c504-359"><xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.</span><span class="sxs-lookup"><span data-stu-id="5c504-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="5c504-360">N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5c504-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c504-361">Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="5c504-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="5c504-362">Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="5c504-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="5c504-363">Quand les [paramètres de configuration](xref:fundamentals/configuration/index) sont isolés par scénario dans des classes distinctes, l’application est conforme à deux principes d’ingénierie logicielle importants :</span><span class="sxs-lookup"><span data-stu-id="5c504-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="5c504-364">Le [principe de séparation d’interface (ISP) ou l’encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): les scénarios (classes) qui dépendent des paramètres de configuration dépendent uniquement des paramètres de configuration qu’ils utilisent.</span><span class="sxs-lookup"><span data-stu-id="5c504-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="5c504-365">[Séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) : les paramètres des différentes parties de l’application ne sont pas dépendants ou associés les uns aux autres.</span><span class="sxs-lookup"><span data-stu-id="5c504-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="5c504-366">Ces options fournissent également un mécanisme de validation des données de configuration.</span><span class="sxs-lookup"><span data-stu-id="5c504-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="5c504-367">Pour plus d'informations, reportez-vous à la section [Validation des options](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="5c504-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="5c504-368">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c504-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5c504-369">Prérequis</span><span class="sxs-lookup"><span data-stu-id="5c504-369">Prerequisites</span></span>

<span data-ttu-id="5c504-370">Référencer le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou ajouter une référence de package au package [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="5c504-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="5c504-371">Interfaces d’options</span><span class="sxs-lookup"><span data-stu-id="5c504-371">Options interfaces</span></span>

<span data-ttu-id="5c504-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> permet de récupérer des options et de gérer les notifications d’options pour les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="5c504-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="5c504-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="5c504-374">Notifications de modifications</span><span class="sxs-lookup"><span data-stu-id="5c504-374">Change notifications</span></span>
* [<span data-ttu-id="5c504-375">Options nommées</span><span class="sxs-lookup"><span data-stu-id="5c504-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="5c504-376">Configuration rechargeable</span><span class="sxs-lookup"><span data-stu-id="5c504-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="5c504-377">Invalidation sélective des options (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="5c504-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="5c504-378">Des scénarios [post-configuration](#options-post-configuration) vous permettent de définir ou de modifier les options après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="5c504-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> est chargée de créer les instances d’options.</span><span class="sxs-lookup"><span data-stu-id="5c504-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="5c504-380">Elle dispose d’une seule méthode <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="5c504-381">L’implémentation par défaut prend toutes les <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> inscrites et exécute toutes les configurations, puis les post-configurations.</span><span class="sxs-lookup"><span data-stu-id="5c504-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="5c504-382">Elle fait la distinction entre <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et <xref:Microsoft.Extensions.Options.IConfigureOptions%601> et n’appelle que l’interface appropriée.</span><span class="sxs-lookup"><span data-stu-id="5c504-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="5c504-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> est utilisée par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour mettre en cache les instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="5c504-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalide les instances des options dans le moniteur afin que la valeur soit recalculée (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="5c504-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="5c504-385">Les valeurs peuvent aussi être introduites manuellement avec <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="5c504-386">La méthode <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> est utilisée quand toutes les instances nommées doivent être recréées à la demande.</span><span class="sxs-lookup"><span data-stu-id="5c504-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="5c504-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est utile dans les scénarios où des options doivent être recalculées à chaque requête.</span><span class="sxs-lookup"><span data-stu-id="5c504-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="5c504-388">Pour plus d’informations, consultez la section [Recharger les données de configuration avec IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="5c504-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="5c504-389"><xref:Microsoft.Extensions.Options.IOptions%601> peut être utilisée pour prendre en charge des options.</span><span class="sxs-lookup"><span data-stu-id="5c504-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="5c504-390">Toutefois, <xref:Microsoft.Extensions.Options.IOptions%601> ne prend pas en charge les scénarios <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> précédents.</span><span class="sxs-lookup"><span data-stu-id="5c504-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="5c504-391">Vous pouvez continuer à utiliser <xref:Microsoft.Extensions.Options.IOptions%601> dans des infrastructures et bibliothèques existantes qui utilisent déjà l’interface <xref:Microsoft.Extensions.Options.IOptions%601> mais ne nécessitent pas les scénarios fournis par <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="5c504-392">Configuration des options générales</span><span class="sxs-lookup"><span data-stu-id="5c504-392">General options configuration</span></span>

<span data-ttu-id="5c504-393">La configuration des options générales est illustrée dans l’exemple 1 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="5c504-394">Une classe d’options doit être non abstraite avec un constructeur public sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="5c504-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="5c504-395">La classe suivante, `MyOptions`, a deux propriétés : `Option1` et `Option2`.</span><span class="sxs-lookup"><span data-stu-id="5c504-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="5c504-396">Définir des valeurs par défaut est facultatif, mais le constructeur de classe dans l’exemple suivant définit la valeur par défaut `Option1`.</span><span class="sxs-lookup"><span data-stu-id="5c504-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="5c504-397">`Option2` a une valeur par défaut définie par l’initialisation directe de la propriété (*Models/MyOptions.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="5c504-398">La classe `MyOptions` est ajoutée au conteneur de service avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et liée à la configuration :</span><span class="sxs-lookup"><span data-stu-id="5c504-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="5c504-399">Le modèle de page suivant utilise une [injection de dépendances de constructeur](xref:mvc/controllers/dependency-injection) avec <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pour accéder aux paramètres (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="5c504-400">Le fichier *appsettings.json* de l’exemple spécifie des valeurs pour `option1` et `option2` :</span><span class="sxs-lookup"><span data-stu-id="5c504-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="5c504-401">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="5c504-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="5c504-402">Quand vous utilisez un <xref:System.Configuration.ConfigurationBuilder> personnalisé pour charger une configuration d’options à partir d’un fichier de paramètres, vérifiez que le chemin de base est correctement défini :</span><span class="sxs-lookup"><span data-stu-id="5c504-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="5c504-403">Vous n’avez pas besoin de définir explicitement le chemin de base quand vous chargez une configuration d’options à partir du fichier de paramètres via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="5c504-404">Configurer des options simples avec un délégué</span><span class="sxs-lookup"><span data-stu-id="5c504-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="5c504-405">La configuration d’options simples avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="5c504-406">Utilisez un délégué pour définir les valeurs des options.</span><span class="sxs-lookup"><span data-stu-id="5c504-406">Use a delegate to set options values.</span></span> <span data-ttu-id="5c504-407">L’exemple d’application utilise la classe `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="5c504-408">Dans le code suivant, un second service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="5c504-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="5c504-409">Il utilise un délégué pour configurer la liaison avec `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="5c504-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="5c504-410">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c504-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="5c504-411">Vous pouvez ajouter plusieurs fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="5c504-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="5c504-412">Des fournisseurs de configuration sont disponibles à partir de packages NuGet et sont appliqués dans l’ordre de leur inscription.</span><span class="sxs-lookup"><span data-stu-id="5c504-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="5c504-413">Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5c504-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="5c504-414">Chaque appel à <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> ajoute un service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="5c504-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="5c504-415">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont toutes deux spécifiées dans *appsettings.json*, mais les valeurs de `Option1` et `Option2` sont remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="5c504-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="5c504-416">Quand plusieurs services de configuration sont activés, la dernière source de configuration spécifiée *gagne* et définit la valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="5c504-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="5c504-417">Quand l’application est exécutée, la méthode `OnGet` du modèle de page retourne une chaîne indiquant les valeurs de la classe d’options :</span><span class="sxs-lookup"><span data-stu-id="5c504-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="5c504-418">Configuration des sous-options</span><span class="sxs-lookup"><span data-stu-id="5c504-418">Suboptions configuration</span></span>

<span data-ttu-id="5c504-419">La configuration des sous-options est illustrée dans l’exemple 3 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="5c504-420">Les applications doivent créer des classes d’options qui appartiennent à des groupes de scénarios spécifiques (classes) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="5c504-421">Les parties de l’application qui requièrent des valeurs de configuration ne doivent avoir accès qu’aux valeurs de configuration qu’elles utilisent.</span><span class="sxs-lookup"><span data-stu-id="5c504-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="5c504-422">Au moment de la liaison des options à la configuration, chaque propriété du type options est liée à une clé de configuration sous la forme `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="5c504-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="5c504-423">Par exemple, la propriété `MyOptions.Option1` est liée à la clé `Option1`, qui est lue à partir de la propriété `option1` dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5c504-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="5c504-424">Dans le code suivant, un troisième service <xref:Microsoft.Extensions.Options.IConfigureOptions%601> est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="5c504-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="5c504-425">Il lie `MySubOptions` à la section `subsection` du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="5c504-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="5c504-426">La `GetSection` méthode requiert l' <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> espace de noms.</span><span class="sxs-lookup"><span data-stu-id="5c504-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="5c504-427">Le fichier *appsettings.json* de l’exemple définit un membre `subsection` avec des clés pour `suboption1` et `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="5c504-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="5c504-428">La classe `MySubOptions` définit des propriétés, `SubOption1` et `SubOption2`, pour conserver les valeurs des options (*Models/MySubOptions.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="5c504-429">La méthode `OnGet` du modèle de page retourne une chaîne avec les valeurs des options (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="5c504-430">Quand l’application est exécutée, la méthode `OnGet` retourne une chaîne indiquant les valeurs de la classe de sous-options :</span><span class="sxs-lookup"><span data-stu-id="5c504-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="5c504-431">Options fournies par un modèle d’affichage ou avec une injection de vue directe</span><span class="sxs-lookup"><span data-stu-id="5c504-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="5c504-432">Les options fournies par un modèle d’affichage ou avec une injection de vue directe sont illustrées dans l’exemple 4 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="5c504-433">Vous pouvez fournir les options dans un modèle d’affichage ou en injectant <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directement dans une vue (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="5c504-434">L’exemple d’application montre comment injecter `IOptionsMonitor<MyOptions>` avec une directive `@inject` :</span><span class="sxs-lookup"><span data-stu-id="5c504-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="5c504-435">Quand l’application est exécutée, les valeurs d’options sont affichées dans la page rendue :</span><span class="sxs-lookup"><span data-stu-id="5c504-435">When the app is run, the options values are shown in the rendered page:</span></span>

![Les valeurs d’options Option1: value1_from_json et Option2: -1 sont chargées à partir du modèle et par injection dans la vue.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="5c504-437">Recharger les données de configuration avec IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="5c504-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="5c504-438">Le rechargement des données de configuration avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est illustré dans l’exemple 5 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="5c504-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> prend en charge le rechargement des options avec une charge de traitement minimale.</span><span class="sxs-lookup"><span data-stu-id="5c504-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="5c504-440">Les options sont calculées une fois par requête quand le système y accède et sont mises en cache pour toute la durée de vie de la requête.</span><span class="sxs-lookup"><span data-stu-id="5c504-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="5c504-441">Dans l’exemple suivant, un <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> est créé à la suite de la modification du fichier *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="5c504-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="5c504-442">Plusieurs demandes adressées au serveur retournent des valeurs constantes fournies par le fichier *appsettings.json* tant que ce dernier n’est pas changé et que la configuration n’est pas rechargée.</span><span class="sxs-lookup"><span data-stu-id="5c504-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="5c504-443">L’image suivante montre les valeurs `option1` et `option2` initiales chargées à partir du fichier *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="5c504-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="5c504-444">Changez les valeurs dans le fichier *appsettings.json* en `value1_from_json UPDATED` et `200`.</span><span class="sxs-lookup"><span data-stu-id="5c504-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="5c504-445">Enregistrez le *appsettings.jsdans* le fichier.</span><span class="sxs-lookup"><span data-stu-id="5c504-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="5c504-446">Actualisez le navigateur pour constater la mise à jour des valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="5c504-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="5c504-447">Prise en charge des options nommées avec IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="5c504-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="5c504-448">La prise en charge des options nommées avec <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> est illustrée dans l’exemple 6 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c504-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="5c504-449">La prise en charge des options nommées permet à l’application de faire la distinction entre les configurations d’options nommées.</span><span class="sxs-lookup"><span data-stu-id="5c504-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="5c504-450">Dans l’exemple d’application, les options nommées sont déclarées avec [OptionsServiceCollectionExtensions.Configurer](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), qui appelle [ConfigureNamedOptions \<TOptions> . Configurez](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="5c504-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="5c504-451">Les options nommées respectent la casse.</span><span class="sxs-lookup"><span data-stu-id="5c504-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="5c504-452">L’exemple d’application accède aux options nommées avec <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="5c504-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="5c504-453">Quand l’exemple d’application est exécuté, les options nommées sont retournées :</span><span class="sxs-lookup"><span data-stu-id="5c504-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="5c504-454">Les valeurs `named_options_1`, issues de la configuration, sont chargées à partir du fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5c504-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="5c504-455">Les valeurs `named_options_2` sont fournies par :</span><span class="sxs-lookup"><span data-stu-id="5c504-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="5c504-456">Le délégué `named_options_2` dans `ConfigureServices` pour `Option1`.</span><span class="sxs-lookup"><span data-stu-id="5c504-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="5c504-457">La valeur par défaut `Option2` fournie par la classe `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="5c504-458">Configurer toutes les options avec la méthode ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="5c504-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="5c504-459">Configurez toutes les instances d’options avec la méthode <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="5c504-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="5c504-460">Le code suivant configure `Option1` pour toutes les instances de configuration ayant une valeur commune.</span><span class="sxs-lookup"><span data-stu-id="5c504-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="5c504-461">Ajoutez le code suivant manuellement à la méthode `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5c504-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="5c504-462">Exécuter l’exemple d’application après avoir ajouté le code produit le résultat suivant :</span><span class="sxs-lookup"><span data-stu-id="5c504-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="5c504-463">Toutes les options sont des instances nommées.</span><span class="sxs-lookup"><span data-stu-id="5c504-463">All options are named instances.</span></span> <span data-ttu-id="5c504-464">Les instances <xref:Microsoft.Extensions.Options.IConfigureOptions%601> existantes sont traitées comme ciblant l’instance `Options.DefaultName`, qui est `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="5c504-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="5c504-465">En outre, <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="5c504-466">L’implémentation par défaut de <xref:Microsoft.Extensions.Options.IOptionsFactory%601> possède une logique qui utilise chaque élément de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="5c504-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="5c504-467">L’option nommée `null` est utilisée pour cibler toutes les instances nommées au lieu d’une instance nommée spécifique (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> et <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> utilisent cette convention).</span><span class="sxs-lookup"><span data-stu-id="5c504-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="5c504-468">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="5c504-468">OptionsBuilder API</span></span>

<span data-ttu-id="5c504-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> permet de configurer des instances `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c504-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="5c504-470">`OptionsBuilder` simplifie la création d’options nommées. En effet, il est le seul paramètre de l’appel `AddOptions<TOptions>(string optionsName)` initial et n’apparaît pas dans les appels ultérieurs.</span><span class="sxs-lookup"><span data-stu-id="5c504-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="5c504-471">La validation des options et les surcharges `ConfigureOptions` qui acceptent des dépendances de service sont uniquement disponibles avec `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5c504-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="5c504-472">Utiliser les services d’injection de dépendances (DI) pour configurer des options</span><span class="sxs-lookup"><span data-stu-id="5c504-472">Use DI services to configure options</span></span>

<span data-ttu-id="5c504-473">Vous pouvez accéder à d’autres services à partir de l’injection de dépendances pendant que vous configurez des options de deux manières différentes :</span><span class="sxs-lookup"><span data-stu-id="5c504-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="5c504-474">Transmettez un délégué de configuration à [configurer](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) sur [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="5c504-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="5c504-475">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) fournit des surcharges de la [configuration](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) qui vous permettent d’utiliser jusqu’à cinq services pour configurer des options :</span><span class="sxs-lookup"><span data-stu-id="5c504-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="5c504-476">Créez votre propre type qui implémente <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ou <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> et inscrit le type en tant que service.</span><span class="sxs-lookup"><span data-stu-id="5c504-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="5c504-477">Nous vous recommandons de transmettre un délégué de configuration à [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), car il est plus complexe de créer un service.</span><span class="sxs-lookup"><span data-stu-id="5c504-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="5c504-478">Créer votre propre type équivaut à ce que fait automatiquement le framework quand vous utilisez [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="5c504-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="5c504-479">L’appel de [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) a pour effet d’inscrire une instance générique temporaire de <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, dont l’un des constructeurs accepte les types de service génériques spécifiés.</span><span class="sxs-lookup"><span data-stu-id="5c504-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="5c504-480">Options de post-configuration</span><span class="sxs-lookup"><span data-stu-id="5c504-480">Options post-configuration</span></span>

<span data-ttu-id="5c504-481">Définissez la post-configuration avec <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5c504-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="5c504-482">La post-configuration s’exécute après chaque configuration de <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :</span><span class="sxs-lookup"><span data-stu-id="5c504-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="5c504-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> permet de post-configurer les options nommées :</span><span class="sxs-lookup"><span data-stu-id="5c504-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="5c504-484">Utilisez <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> pour post-configurer toutes les instances de configuration :</span><span class="sxs-lookup"><span data-stu-id="5c504-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="5c504-485">Accès aux options au démarrage</span><span class="sxs-lookup"><span data-stu-id="5c504-485">Accessing options during startup</span></span>

<span data-ttu-id="5c504-486"><xref:Microsoft.Extensions.Options.IOptions%601> et <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> peuvent être utilisées dans `Startup.Configure`, dans la mesure où les services sont générés avant que la méthode `Configure` ne s’exécute.</span><span class="sxs-lookup"><span data-stu-id="5c504-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="5c504-487">N’utilisez pas <xref:Microsoft.Extensions.Options.IOptions%601> ou <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5c504-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c504-488">Un état d’options incohérent peut exister en raison de l’ordre des inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="5c504-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5c504-489">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5c504-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
