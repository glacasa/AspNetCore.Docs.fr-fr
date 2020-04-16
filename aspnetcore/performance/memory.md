---
title: Gestion de la mémoire et modèles dans ASP.NET Core
author: rick-anderson
description: Découvrez comment la mémoire est gérée dans ASP.NET Core et comment fonctionne le collecteur d’ordures (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440946"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="5f090-103">Gestion de la mémoire et collecte des ordures (GC) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f090-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="5f090-104">Par [Sébastien Ros](https://github.com/sebastienros) et Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f090-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5f090-105">La gestion de la mémoire est complexe, même dans un cadre géré comme .NET.</span><span class="sxs-lookup"><span data-stu-id="5f090-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="5f090-106">L’analyse et la compréhension des problèmes de mémoire peuvent être difficiles.</span><span class="sxs-lookup"><span data-stu-id="5f090-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="5f090-107">Cet article :</span><span class="sxs-lookup"><span data-stu-id="5f090-107">This article:</span></span>

* <span data-ttu-id="5f090-108">A été motivé par de nombreuses *fuites* de mémoire et *GC ne fonctionne pas* les problèmes.</span><span class="sxs-lookup"><span data-stu-id="5f090-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="5f090-109">La plupart de ces problèmes ont été causés par le fait de ne pas comprendre comment fonctionne la consommation de mémoire dans .NET Core, ou de ne pas comprendre comment il est mesuré.</span><span class="sxs-lookup"><span data-stu-id="5f090-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="5f090-110">Démontre l’utilisation problématique de la mémoire, et suggère des approches alternatives.</span><span class="sxs-lookup"><span data-stu-id="5f090-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="5f090-111">Fonctionnement de la collecte des ordures (GC) dans .NET Core</span><span class="sxs-lookup"><span data-stu-id="5f090-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="5f090-112">Le GC alloue des segments de tas où chaque segment est une gamme contigu de mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="5f090-113">Les objets placés dans le tas sont classés en l’une des 3 générations : 0, 1 ou 2.</span><span class="sxs-lookup"><span data-stu-id="5f090-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="5f090-114">La génération détermine la fréquence à laquelle le GC tente de libérer de la mémoire sur des objets gérés qui ne sont plus référencés par l’application.</span><span class="sxs-lookup"><span data-stu-id="5f090-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="5f090-115">Les générations numérotées inférieures sont GC’d plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="5f090-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="5f090-116">Les objets sont déplacés d’une génération à l’autre en fonction de leur vie.</span><span class="sxs-lookup"><span data-stu-id="5f090-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="5f090-117">Comme les objets vivent plus longtemps, ils sont déplacés dans une génération supérieure.</span><span class="sxs-lookup"><span data-stu-id="5f090-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="5f090-118">Comme mentionné précédemment, les générations supérieures sont GC’d moins souvent.</span><span class="sxs-lookup"><span data-stu-id="5f090-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="5f090-119">Les objets de courte durée restent toujours dans la génération 0.</span><span class="sxs-lookup"><span data-stu-id="5f090-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="5f090-120">Par exemple, les objets qui sont référencés pendant la durée d’une demande web sont de courte durée.</span><span class="sxs-lookup"><span data-stu-id="5f090-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="5f090-121">Les [singletons de niveau d’application](xref:fundamentals/dependency-injection#service-lifetimes) migrent généralement vers la génération 2.</span><span class="sxs-lookup"><span data-stu-id="5f090-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="5f090-122">Lorsqu’une application ASP.NET Core démarre, le GC :</span><span class="sxs-lookup"><span data-stu-id="5f090-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="5f090-123">Réserve un peu de mémoire pour les segments de tas initiaux.</span><span class="sxs-lookup"><span data-stu-id="5f090-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="5f090-124">Engage une petite partie de mémoire lorsque le temps d’exécution est chargé.</span><span class="sxs-lookup"><span data-stu-id="5f090-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="5f090-125">Les allocations de mémoire précédentes sont effectuées pour des raisons de performance.</span><span class="sxs-lookup"><span data-stu-id="5f090-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="5f090-126">L’avantage de performance vient des segments de tas dans la mémoire contigu.</span><span class="sxs-lookup"><span data-stu-id="5f090-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="5f090-127">Appelez GC. Recueillir</span><span class="sxs-lookup"><span data-stu-id="5f090-127">Call GC.Collect</span></span>

<span data-ttu-id="5f090-128">Appel [GC. Recueillir](xref:System.GC.Collect*) explicitement:</span><span class="sxs-lookup"><span data-stu-id="5f090-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="5f090-129">Ne doit **pas** être fait par la production ASP.NET applications Core.</span><span class="sxs-lookup"><span data-stu-id="5f090-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="5f090-130">Est utile lors de l’étude des fuites de mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="5f090-131">Lors de l’enquête, vérifie que le GC a supprimé tous les objets suspendus de la mémoire afin que la mémoire puisse être mesurée.</span><span class="sxs-lookup"><span data-stu-id="5f090-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="5f090-132">Analyse de l’utilisation de la mémoire d’une application</span><span class="sxs-lookup"><span data-stu-id="5f090-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="5f090-133">Des outils dédiés peuvent aider à analyser l’utilisation de la mémoire :</span><span class="sxs-lookup"><span data-stu-id="5f090-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="5f090-134">Compter les références d’objets</span><span class="sxs-lookup"><span data-stu-id="5f090-134">Counting object references</span></span>
- <span data-ttu-id="5f090-135">Mesurer l’impact de la CCG sur l’utilisation du processeur</span><span class="sxs-lookup"><span data-stu-id="5f090-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="5f090-136">Mesurer l’espace mémoire utilisé pour chaque génération</span><span class="sxs-lookup"><span data-stu-id="5f090-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="5f090-137">Utilisez les outils suivants pour analyser l’utilisation de la mémoire :</span><span class="sxs-lookup"><span data-stu-id="5f090-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="5f090-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Peut être utilisé sur les machines de production.</span><span class="sxs-lookup"><span data-stu-id="5f090-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="5f090-139">Analyser l’utilisation de la mémoire sans le débbugger Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f090-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="5f090-140">Profiler l’utilisation de la mémoire dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f090-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="5f090-141">Détection des problèmes de mémoire</span><span class="sxs-lookup"><span data-stu-id="5f090-141">Detecting memory issues</span></span>

<span data-ttu-id="5f090-142">Task Manager peut être utilisé pour avoir une idée de la quantité de mémoire qu’une application ASP.NET utilise.</span><span class="sxs-lookup"><span data-stu-id="5f090-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="5f090-143">La valeur de mémoire de Task Manager :</span><span class="sxs-lookup"><span data-stu-id="5f090-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="5f090-144">Représente la quantité de mémoire utilisée par le processus ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5f090-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="5f090-145">Inclut les objets vivants de l’application et d’autres consommateurs de mémoire tels que l’utilisation de la mémoire native.</span><span class="sxs-lookup"><span data-stu-id="5f090-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="5f090-146">Si la valeur de mémoire task Manager augmente indéfiniment et ne s’aplatit jamais, l’application a une fuite de mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="5f090-147">Les sections suivantes démontrent et expliquent plusieurs modèles d’utilisation de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="5f090-148">Exemple d’application d’utilisation de la mémoire d’affichage</span><span class="sxs-lookup"><span data-stu-id="5f090-148">Sample display memory usage app</span></span>

<span data-ttu-id="5f090-149">[L’application d’échantillon MemoryLeak](https://github.com/sebastienros/memoryleak) est disponible sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="5f090-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="5f090-150">L’application MemoryLeak :</span><span class="sxs-lookup"><span data-stu-id="5f090-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="5f090-151">Inclut un contrôleur diagnostique qui recueille la mémoire en temps réel et les données GC pour l’application.</span><span class="sxs-lookup"><span data-stu-id="5f090-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="5f090-152">A une page Index qui affiche la mémoire et les données GC.</span><span class="sxs-lookup"><span data-stu-id="5f090-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="5f090-153">La page Index est actualisée chaque seconde.</span><span class="sxs-lookup"><span data-stu-id="5f090-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="5f090-154">Contient un contrôleur API qui fournit divers modèles de charge de mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="5f090-155">N’est pas un outil pris en charge, cependant, il peut être utilisé pour afficher des modèles d’utilisation de la mémoire des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f090-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="5f090-156">Exécutez MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="5f090-156">Run MemoryLeak.</span></span> <span data-ttu-id="5f090-157">La mémoire allouée augmente lentement jusqu’à ce qu’un GC se produise.</span><span class="sxs-lookup"><span data-stu-id="5f090-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="5f090-158">La mémoire augmente parce que l’outil alloue un objet personnalisé pour capturer les données.</span><span class="sxs-lookup"><span data-stu-id="5f090-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="5f090-159">L’image suivante montre la page MemoryLeak Index lorsqu’un GC Gen 0 se produit.</span><span class="sxs-lookup"><span data-stu-id="5f090-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="5f090-160">Le graphique affiche 0 RPS (demandes par seconde) parce qu’aucun critère d’évaluation de l’API du contrôleur aPI n’a été appelé.</span><span class="sxs-lookup"><span data-stu-id="5f090-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![graphique précédent](memory/_static/0RPS.png)

<span data-ttu-id="5f090-162">Le graphique affiche deux valeurs pour l’utilisation de la mémoire :</span><span class="sxs-lookup"><span data-stu-id="5f090-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="5f090-163">Alloué : la quantité de mémoire occupée par des objets gérés</span><span class="sxs-lookup"><span data-stu-id="5f090-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="5f090-164">[Ensemble](/windows/win32/memory/working-set)de travail : L’ensemble de pages dans l’espace d’adresse virtuel du processus qui résident actuellement dans la mémoire physique.</span><span class="sxs-lookup"><span data-stu-id="5f090-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="5f090-165">L’ensemble de travail affiché est la même valeur Task Manager affiche.</span><span class="sxs-lookup"><span data-stu-id="5f090-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="5f090-166">Objets transitoires</span><span class="sxs-lookup"><span data-stu-id="5f090-166">Transient objects</span></span>

<span data-ttu-id="5f090-167">L’API suivante crée une instance de 10 KB String et la renvoie au client.</span><span class="sxs-lookup"><span data-stu-id="5f090-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="5f090-168">À chaque demande, un nouvel objet est attribué en mémoire et écrit à la réponse.</span><span class="sxs-lookup"><span data-stu-id="5f090-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="5f090-169">Les cordes sont stockées sous forme de caractères UTF-16 en .NET de sorte que chaque personnage prend 2 octets dans la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="5f090-170">Le graphique suivant est généré avec une charge relativement faible pour montrer comment les allocations de mémoire sont affectées par le GC.</span><span class="sxs-lookup"><span data-stu-id="5f090-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![graphique précédent](memory/_static/bigstring.png)

<span data-ttu-id="5f090-172">Le graphique précédent montre :</span><span class="sxs-lookup"><span data-stu-id="5f090-172">The preceding chart shows:</span></span>

* <span data-ttu-id="5f090-173">4K RPS (Demandes par seconde).</span><span class="sxs-lookup"><span data-stu-id="5f090-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="5f090-174">Les collections de génération 0 GC ont lieu environ toutes les deux secondes.</span><span class="sxs-lookup"><span data-stu-id="5f090-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="5f090-175">L’ensemble de travail est constant à environ 500 Mo.</span><span class="sxs-lookup"><span data-stu-id="5f090-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="5f090-176">Le processeur est de 12%.</span><span class="sxs-lookup"><span data-stu-id="5f090-176">CPU is 12%.</span></span>
* <span data-ttu-id="5f090-177">La consommation et la libération de mémoire (par GC) sont stables.</span><span class="sxs-lookup"><span data-stu-id="5f090-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="5f090-178">Le graphique suivant est pris au débit maximum qui peut être manipulé par la machine.</span><span class="sxs-lookup"><span data-stu-id="5f090-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![graphique précédent](memory/_static/bigstring2.png)

<span data-ttu-id="5f090-180">Le graphique précédent montre :</span><span class="sxs-lookup"><span data-stu-id="5f090-180">The preceding chart shows:</span></span>

* <span data-ttu-id="5f090-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="5f090-181">22K RPS</span></span>
* <span data-ttu-id="5f090-182">Les collections de génération 0 GC ont lieu plusieurs fois par seconde.</span><span class="sxs-lookup"><span data-stu-id="5f090-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="5f090-183">Les collections de génération 1 sont déclenchées parce que l’application a alloué beaucoup plus de mémoire par seconde.</span><span class="sxs-lookup"><span data-stu-id="5f090-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="5f090-184">L’ensemble de travail est constant à environ 500 Mo.</span><span class="sxs-lookup"><span data-stu-id="5f090-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="5f090-185">Le processeur est de 33%.</span><span class="sxs-lookup"><span data-stu-id="5f090-185">CPU is 33%.</span></span>
* <span data-ttu-id="5f090-186">La consommation et la libération de mémoire (par GC) sont stables.</span><span class="sxs-lookup"><span data-stu-id="5f090-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="5f090-187">Le CPU (33%) n’est pas surutilisé, c’est pourquoi la collecte des ordures peut suivre un nombre élevé d’allocations.</span><span class="sxs-lookup"><span data-stu-id="5f090-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="5f090-188">Workstation GC vs Server GC</span><span class="sxs-lookup"><span data-stu-id="5f090-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="5f090-189">Le collecteur d’ordures .NET a deux modes différents:</span><span class="sxs-lookup"><span data-stu-id="5f090-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="5f090-190">**Workstation GC**: Optimisé pour le bureau.</span><span class="sxs-lookup"><span data-stu-id="5f090-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="5f090-191">**Serveur GC**.</span><span class="sxs-lookup"><span data-stu-id="5f090-191">**Server GC**.</span></span> <span data-ttu-id="5f090-192">Le GC par défaut pour ASP.NET applications Core.</span><span class="sxs-lookup"><span data-stu-id="5f090-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="5f090-193">Optimisé pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="5f090-193">Optimized for the server.</span></span>

<span data-ttu-id="5f090-194">Le mode GC peut être défini explicitement dans le fichier du projet ou dans le fichier *runtimeconfig.json* de l’application publiée.</span><span class="sxs-lookup"><span data-stu-id="5f090-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="5f090-195">La balisage `ServerGarbageCollection` suivante montre l’établissement dans le dossier du projet :</span><span class="sxs-lookup"><span data-stu-id="5f090-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="5f090-196">Pour `ServerGarbageCollection` modifier le fichier du projet, l’application doit être reconstruite.</span><span class="sxs-lookup"><span data-stu-id="5f090-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="5f090-197">**Note:** La collecte des ordures du serveur **n’est pas** disponible sur les machines d’un seul cœur.</span><span class="sxs-lookup"><span data-stu-id="5f090-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="5f090-198">Pour plus d’informations, consultez <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="5f090-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="5f090-199">L’image suivante montre le profil de mémoire sous un RPS 5K à l’aide du Workstation GC.</span><span class="sxs-lookup"><span data-stu-id="5f090-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![graphique précédent](memory/_static/workstation.png)

<span data-ttu-id="5f090-201">Les différences entre ce graphique et la version serveur sont significatives :</span><span class="sxs-lookup"><span data-stu-id="5f090-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="5f090-202">L’ensemble de travail passe de 500 Mo à 70 Mo.</span><span class="sxs-lookup"><span data-stu-id="5f090-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="5f090-203">Le GC fait des collections de génération 0 plusieurs fois par seconde au lieu de toutes les deux secondes.</span><span class="sxs-lookup"><span data-stu-id="5f090-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="5f090-204">GC passe de 300 Mo à 10 Mo.</span><span class="sxs-lookup"><span data-stu-id="5f090-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="5f090-205">Sur un environnement de serveur web typique, l’utilisation du processeur est plus importante que la mémoire, donc le Server GC est mieux.</span><span class="sxs-lookup"><span data-stu-id="5f090-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="5f090-206">Si l’utilisation de la mémoire est élevée et que l’utilisation du processeur est relativement faible, le Workstation GC pourrait être plus performant.</span><span class="sxs-lookup"><span data-stu-id="5f090-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="5f090-207">Par exemple, la haute densité hébergeant plusieurs applications Web où la mémoire est rare.</span><span class="sxs-lookup"><span data-stu-id="5f090-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="5f090-208">GC à l’aide de Docker et de petits conteneurs</span><span class="sxs-lookup"><span data-stu-id="5f090-208">GC using Docker and small containers</span></span>

<span data-ttu-id="5f090-209">Lorsque plusieurs applications conteneurisées sont en cours d’exécution sur une machine, Workstation GC peut être plus préformant que Server GC.</span><span class="sxs-lookup"><span data-stu-id="5f090-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="5f090-210">Pour plus d’informations, voir [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and Running with Server [GC in a Small Container Scenario Part 1 - Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="5f090-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="5f090-211">Références d’objets persistants</span><span class="sxs-lookup"><span data-stu-id="5f090-211">Persistent object references</span></span>

<span data-ttu-id="5f090-212">Le GC ne peut pas libérer les objets qui sont référencés.</span><span class="sxs-lookup"><span data-stu-id="5f090-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="5f090-213">Les objets référencés mais qui n’ont plus besoin entraînent une fuite de mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="5f090-214">Si l’application alloue fréquemment des objets et ne les libère pas après qu’ils ne soient plus nécessaires, l’utilisation de la mémoire augmentera avec le temps.</span><span class="sxs-lookup"><span data-stu-id="5f090-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="5f090-215">L’API suivante crée une instance de 10 KB String et la renvoie au client.</span><span class="sxs-lookup"><span data-stu-id="5f090-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="5f090-216">La différence avec l’exemple précédent est que cette instance est référencée par un membre statique, ce qui signifie qu’il n’est jamais disponible pour la collecte.</span><span class="sxs-lookup"><span data-stu-id="5f090-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="5f090-217">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="5f090-217">The preceding code:</span></span>

* <span data-ttu-id="5f090-218">Est un exemple d’une fuite de mémoire typique.</span><span class="sxs-lookup"><span data-stu-id="5f090-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="5f090-219">Avec les appels fréquents, provoque des augmentations de la mémoire d’application jusqu’à ce que le processus se bloque à une `OutOfMemory` exception près.</span><span class="sxs-lookup"><span data-stu-id="5f090-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![graphique précédent](memory/_static/eternal.png)

<span data-ttu-id="5f090-221">Dans l’image précédente:</span><span class="sxs-lookup"><span data-stu-id="5f090-221">In the preceding image:</span></span>

* <span data-ttu-id="5f090-222">L’essai `/api/staticstring` de charge le point de terminaison provoque une augmentation linéaire de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="5f090-223">Le GC tente de libérer la mémoire à mesure que la pression de la mémoire augmente, en appelant une collection de génération 2.</span><span class="sxs-lookup"><span data-stu-id="5f090-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="5f090-224">Le GC ne peut pas libérer la mémoire fuite.</span><span class="sxs-lookup"><span data-stu-id="5f090-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="5f090-225">Les ensembles alloués et de travail augmentent avec le temps.</span><span class="sxs-lookup"><span data-stu-id="5f090-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="5f090-226">Certains scénarios, tels que la mise en cache, nécessitent des références d’objets à tenir jusqu’à ce que la pression de mémoire les oblige à être libérés.</span><span class="sxs-lookup"><span data-stu-id="5f090-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="5f090-227">La <xref:System.WeakReference> classe peut être utilisée pour ce type de code de mise en cache.</span><span class="sxs-lookup"><span data-stu-id="5f090-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="5f090-228">Un `WeakReference` objet est recueilli sous la pression de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="5f090-229">La mise <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> en `WeakReference`œuvre par défaut des utilisations .</span><span class="sxs-lookup"><span data-stu-id="5f090-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="5f090-230">Mémoire autochtone</span><span class="sxs-lookup"><span data-stu-id="5f090-230">Native memory</span></span>

<span data-ttu-id="5f090-231">Certains objets .NET Core s’appuient sur la mémoire indigène.</span><span class="sxs-lookup"><span data-stu-id="5f090-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="5f090-232">La mémoire autochtone ne peut **pas** être recueillie par le GC.</span><span class="sxs-lookup"><span data-stu-id="5f090-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="5f090-233">L’objet .NET utilisant la mémoire indigène doit le libérer à l’aide de code natif.</span><span class="sxs-lookup"><span data-stu-id="5f090-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="5f090-234">.NET fournit <xref:System.IDisposable> l’interface pour permettre aux développeurs de libérer la mémoire native.</span><span class="sxs-lookup"><span data-stu-id="5f090-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="5f090-235">Même <xref:System.IDisposable.Dispose*> s’il n’est `Dispose` pas appelé, les classes correctement mises en œuvre appellent lorsque le [finalisateur s’exécute.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)</span><span class="sxs-lookup"><span data-stu-id="5f090-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="5f090-236">Examinons le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="5f090-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="5f090-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) est une classe gérée, de sorte que toute instance sera recueillie à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="5f090-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="5f090-238">L’image suivante montre le profil `fileprovider` de mémoire tout en invoquant l’API en permanence.</span><span class="sxs-lookup"><span data-stu-id="5f090-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![graphique précédent](memory/_static/fileprovider.png)

<span data-ttu-id="5f090-240">Le graphique précédent montre un problème évident avec la mise en œuvre de cette classe, car il ne cesse d’augmenter l’utilisation de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="5f090-241">Il s’agit d’un problème connu qui fait l’objet d’un suivi dans [ce dossier](https://github.com/dotnet/aspnetcore/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="5f090-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="5f090-242">La même fuite pourrait se produire dans le code utilisateur, par l’un des suivants:</span><span class="sxs-lookup"><span data-stu-id="5f090-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="5f090-243">Ne pas libérer la classe correctement.</span><span class="sxs-lookup"><span data-stu-id="5f090-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="5f090-244">Oubliant d’invoquer la `Dispose`méthode des objets dépendants qui doivent être éliminés.</span><span class="sxs-lookup"><span data-stu-id="5f090-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="5f090-245">Grand tas d’objets</span><span class="sxs-lookup"><span data-stu-id="5f090-245">Large objects heap</span></span>

<span data-ttu-id="5f090-246">L’allocation fréquente de mémoire/cycles libres peut fragmenter la mémoire, en particulier lors de l’attribution de gros morceaux de mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="5f090-247">Les objets sont attribués dans des blocs de mémoire contigus.</span><span class="sxs-lookup"><span data-stu-id="5f090-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="5f090-248">Pour atténuer la fragmentation, lorsque le GC libère la mémoire, il essaie de le défragmenter.</span><span class="sxs-lookup"><span data-stu-id="5f090-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="5f090-249">Ce processus est appelé **compactage**.</span><span class="sxs-lookup"><span data-stu-id="5f090-249">This process is called **compaction**.</span></span> <span data-ttu-id="5f090-250">Compaction implique le déplacement d’objets.</span><span class="sxs-lookup"><span data-stu-id="5f090-250">Compaction involves moving objects.</span></span> <span data-ttu-id="5f090-251">Le déplacement de gros objets impose une pénalité de performance.</span><span class="sxs-lookup"><span data-stu-id="5f090-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="5f090-252">Pour cette raison, le GC crée une zone de mémoire spéciale pour _les grands_ objets, appelé le tas de [gros objets](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="5f090-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="5f090-253">Les objets de plus de 85 000 octets (environ 83 KB) sont :</span><span class="sxs-lookup"><span data-stu-id="5f090-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="5f090-254">Placé sur le LOH.</span><span class="sxs-lookup"><span data-stu-id="5f090-254">Placed on the LOH.</span></span>
* <span data-ttu-id="5f090-255">Pas compacté.</span><span class="sxs-lookup"><span data-stu-id="5f090-255">Not compacted.</span></span>
* <span data-ttu-id="5f090-256">Recueillis au cours de la génération 2 GCs.</span><span class="sxs-lookup"><span data-stu-id="5f090-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="5f090-257">Lorsque le LOH est plein, le GC déclenchera une collection de génération 2.</span><span class="sxs-lookup"><span data-stu-id="5f090-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="5f090-258">Collections de génération 2 :</span><span class="sxs-lookup"><span data-stu-id="5f090-258">Generation 2 collections:</span></span>

* <span data-ttu-id="5f090-259">Sont intrinsèquement lents.</span><span class="sxs-lookup"><span data-stu-id="5f090-259">Are inherently slow.</span></span>
* <span data-ttu-id="5f090-260">En outre, encourir le coût de déclencher une collection sur toutes les autres générations.</span><span class="sxs-lookup"><span data-stu-id="5f090-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="5f090-261">Le code suivant compacte immédiatement le LOH :</span><span class="sxs-lookup"><span data-stu-id="5f090-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="5f090-262">Voir <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> pour plus d’informations sur le compactage du LOH.</span><span class="sxs-lookup"><span data-stu-id="5f090-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="5f090-263">Dans les conteneurs utilisant .NET Core 3.0 et plus tard, le LOH est automatiquement compacté.</span><span class="sxs-lookup"><span data-stu-id="5f090-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="5f090-264">L’API suivante qui illustre ce comportement :</span><span class="sxs-lookup"><span data-stu-id="5f090-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="5f090-265">Le graphique suivant montre le `/api/loh/84975` profil de mémoire d’appeler le point de terminaison, sous la charge maximale:</span><span class="sxs-lookup"><span data-stu-id="5f090-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![graphique précédent](memory/_static/loh1.png)

<span data-ttu-id="5f090-267">Le graphique suivant montre le `/api/loh/84976` profil de mémoire d’appeler le point de terminaison, en allouant *un seul byte de plus*:</span><span class="sxs-lookup"><span data-stu-id="5f090-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![graphique précédent](memory/_static/loh2.png)

<span data-ttu-id="5f090-269">Remarque : `byte[]` La structure a des octets aériens.</span><span class="sxs-lookup"><span data-stu-id="5f090-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="5f090-270">C’est pourquoi 84 976 octets déclenchent la limite de 85 000.</span><span class="sxs-lookup"><span data-stu-id="5f090-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="5f090-271">Comparaison des deux graphiques précédents :</span><span class="sxs-lookup"><span data-stu-id="5f090-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="5f090-272">L’ensemble de travail est similaire pour les deux scénarios, environ 450 Mo.</span><span class="sxs-lookup"><span data-stu-id="5f090-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="5f090-273">Les demandes sous LOH (84 975 octets) montrent principalement des collections de génération 0.</span><span class="sxs-lookup"><span data-stu-id="5f090-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="5f090-274">Les demandes sur LOH génèrent des collections de génération 2 constantes.</span><span class="sxs-lookup"><span data-stu-id="5f090-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="5f090-275">Les collections de génération 2 sont chères.</span><span class="sxs-lookup"><span data-stu-id="5f090-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="5f090-276">Plus de processeur est nécessaire et le débit baisse de près de 50%.</span><span class="sxs-lookup"><span data-stu-id="5f090-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="5f090-277">Les gros objets temporaires sont particulièrement problématiques parce qu’ils causent des OGM gen2.</span><span class="sxs-lookup"><span data-stu-id="5f090-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="5f090-278">Pour une performance maximale, l’utilisation d’objets voluminés doit être réduite au minimum.</span><span class="sxs-lookup"><span data-stu-id="5f090-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="5f090-279">Si possible, divisez de gros objets.</span><span class="sxs-lookup"><span data-stu-id="5f090-279">If possible, split up large objects.</span></span> <span data-ttu-id="5f090-280">Par exemple, [Response Caching](xref:performance/caching/response) middleware dans ASP.NET Core divise les entrées de cache en blocs de moins de 85 000 octets.</span><span class="sxs-lookup"><span data-stu-id="5f090-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="5f090-281">Les liens suivants montrent l’approche ASP.NET Core pour garder les objets sous la limite LOH :</span><span class="sxs-lookup"><span data-stu-id="5f090-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="5f090-282">ResponseCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="5f090-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="5f090-283">ResponseCaching/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="5f090-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="5f090-284">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="5f090-284">For more information, see:</span></span>

* [<span data-ttu-id="5f090-285">Grand tas d’objets découvert</span><span class="sxs-lookup"><span data-stu-id="5f090-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="5f090-286">Grand tas d’objets</span><span class="sxs-lookup"><span data-stu-id="5f090-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="5f090-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="5f090-287">HttpClient</span></span>

<span data-ttu-id="5f090-288">Une utilisation <xref:System.Net.Http.HttpClient> incorrecte peut entraîner une fuite de ressources.</span><span class="sxs-lookup"><span data-stu-id="5f090-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="5f090-289">Ressources système, telles que les connexions de base de données, les prises, les poignées de fichiers, etc. :</span><span class="sxs-lookup"><span data-stu-id="5f090-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="5f090-290">Sont plus rares que la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="5f090-291">Sont plus problématiques lorsqu’ils fuient que la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5f090-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="5f090-292">Expérimentés développeurs .NET <xref:System.IDisposable.Dispose*> savent faire <xref:System.IDisposable>appel à des objets qui implémenter .</span><span class="sxs-lookup"><span data-stu-id="5f090-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="5f090-293">Le fait de `IDisposable` ne pas éliminer les objets qui implémentent entraîne généralement une fuite de mémoire ou des fuites de ressources système.</span><span class="sxs-lookup"><span data-stu-id="5f090-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="5f090-294">`HttpClient`met `IDisposable`en œuvre, mais ne doit **pas** être éliminé sur chaque invocation.</span><span class="sxs-lookup"><span data-stu-id="5f090-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="5f090-295">Il `HttpClient` faut plutôt réutiliser.</span><span class="sxs-lookup"><span data-stu-id="5f090-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="5f090-296">Le critère d’évaluation suivant `HttpClient` crée et dispose d’une nouvelle instance sur chaque demande :</span><span class="sxs-lookup"><span data-stu-id="5f090-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="5f090-297">Sous charge, les messages d’erreur suivants sont enregistrés :</span><span class="sxs-lookup"><span data-stu-id="5f090-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="5f090-298">Même si `HttpClient` les instances sont éliminées, la connexion réseau réelle prend un certain temps à être libérée par le système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="5f090-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="5f090-299">En créant continuellement de nouvelles connexions, _l’épuisement des ports_ se produit.</span><span class="sxs-lookup"><span data-stu-id="5f090-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="5f090-300">Chaque connexion client nécessite son propre port client.</span><span class="sxs-lookup"><span data-stu-id="5f090-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="5f090-301">Une façon de prévenir l’épuisement portuaire `HttpClient` est de réutiliser le même cas :</span><span class="sxs-lookup"><span data-stu-id="5f090-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="5f090-302">L’instance `HttpClient` est libérée lorsque l’application s’arrête.</span><span class="sxs-lookup"><span data-stu-id="5f090-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="5f090-303">Cet exemple montre que toutes les ressources jetables ne doivent pas être éliminées après chaque utilisation.</span><span class="sxs-lookup"><span data-stu-id="5f090-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="5f090-304">Voir ce qui suit pour une meilleure `HttpClient` façon de gérer la durée de vie d’une instance:</span><span class="sxs-lookup"><span data-stu-id="5f090-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="5f090-305">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="5f090-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="5f090-306">BLOG d’usine HTTPClient</span><span class="sxs-lookup"><span data-stu-id="5f090-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="5f090-307">Mise en commun d’objets</span><span class="sxs-lookup"><span data-stu-id="5f090-307">Object pooling</span></span>

<span data-ttu-id="5f090-308">L’exemple précédent `HttpClient` a montré comment l’instance peut être rendue statique et réutilisée par toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="5f090-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="5f090-309">La réutilisation empêche de manquer de ressources.</span><span class="sxs-lookup"><span data-stu-id="5f090-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="5f090-310">Mise en commun des objets :</span><span class="sxs-lookup"><span data-stu-id="5f090-310">Object pooling:</span></span>

* <span data-ttu-id="5f090-311">Utilise le modèle de réutilisation.</span><span class="sxs-lookup"><span data-stu-id="5f090-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="5f090-312">Est conçu pour les objets qui sont coûteux à créer.</span><span class="sxs-lookup"><span data-stu-id="5f090-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="5f090-313">Une piscine est une collection d’objets pré-initialisés qui peuvent être réservés et libérés sur les fils.</span><span class="sxs-lookup"><span data-stu-id="5f090-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="5f090-314">Les pools peuvent définir des règles d’attribution telles que des limites, des tailles prédéfinies ou un taux de croissance.</span><span class="sxs-lookup"><span data-stu-id="5f090-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="5f090-315">Le paquet NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contient des classes qui aident à gérer ces pools.</span><span class="sxs-lookup"><span data-stu-id="5f090-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="5f090-316">Le point de terminaison `byte` suivant de l’API instantanément un tampon qui est rempli de nombres aléatoires sur chaque demande:</span><span class="sxs-lookup"><span data-stu-id="5f090-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="5f090-317">L’affichage graphique suivant appelant l’API précédente avec une charge modérée :</span><span class="sxs-lookup"><span data-stu-id="5f090-317">The following chart display calling the preceding API with moderate load:</span></span>

![graphique précédent](memory/_static/array.png)

<span data-ttu-id="5f090-319">Dans le tableau précédent, les collections de génération 0 se produisent environ une fois par seconde.</span><span class="sxs-lookup"><span data-stu-id="5f090-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="5f090-320">Le code précédent peut être optimisé `byte` en mettant en commun le tampon en utilisant [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="5f090-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="5f090-321">Une instance statique est réutilisée à travers les demandes.</span><span class="sxs-lookup"><span data-stu-id="5f090-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="5f090-322">Ce qui est différent avec cette approche, c’est qu’un objet mis en commun est retourné de l’API.</span><span class="sxs-lookup"><span data-stu-id="5f090-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="5f090-323">Cela signifie:</span><span class="sxs-lookup"><span data-stu-id="5f090-323">That means:</span></span>

* <span data-ttu-id="5f090-324">L’objet est hors de votre contrôle dès que vous revenez de la méthode.</span><span class="sxs-lookup"><span data-stu-id="5f090-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="5f090-325">Vous ne pouvez pas libérer l’objet.</span><span class="sxs-lookup"><span data-stu-id="5f090-325">You can't release the object.</span></span>

<span data-ttu-id="5f090-326">Pour configurer l’élimination de l’objet :</span><span class="sxs-lookup"><span data-stu-id="5f090-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="5f090-327">Encapsulez le tableau mis en commun dans un objet jetable.</span><span class="sxs-lookup"><span data-stu-id="5f090-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="5f090-328">Enregistrez l’objet mis en commun avec [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="5f090-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="5f090-329">`RegisterForDispose`s’occupera `Dispose`d’appeler l’objet cible afin qu’il ne soit publié que lorsque la demande HTTP est terminée.</span><span class="sxs-lookup"><span data-stu-id="5f090-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="5f090-330">L’application de la même charge que la version non mise en commun se traduit par le graphique suivant :</span><span class="sxs-lookup"><span data-stu-id="5f090-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![graphique précédent](memory/_static/pooledarray.png)

<span data-ttu-id="5f090-332">La principale différence est attribuée aux octets, et par conséquent beaucoup moins de collections de génération 0.</span><span class="sxs-lookup"><span data-stu-id="5f090-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f090-333">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5f090-333">Additional resources</span></span>

* [<span data-ttu-id="5f090-334">Garbage collection</span><span class="sxs-lookup"><span data-stu-id="5f090-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="5f090-335">Comprendre différents modes GC avec Concurrency Visualizer</span><span class="sxs-lookup"><span data-stu-id="5f090-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="5f090-336">Grand tas d’objets découvert</span><span class="sxs-lookup"><span data-stu-id="5f090-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="5f090-337">Grand tas d’objets</span><span class="sxs-lookup"><span data-stu-id="5f090-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
