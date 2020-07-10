---
title: Filtres dans ASP.NET Core
author: Rick-Anderson
description: Découvrez comment les filtres fonctionnent et comment les utiliser dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 0141ad2df5216183424980a6ca50bf6bcd64ade5
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213064"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="0b6e3-103">Filtres dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b6e3-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b6e3-104">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0b6e3-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0b6e3-105">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="0b6e3-106">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="0b6e3-107">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="0b6e3-108">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="0b6e3-109">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="0b6e3-110">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="0b6e3-111">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="0b6e3-112">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="0b6e3-113">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="0b6e3-114">Les filtres ne fonctionnent pas directement avec les [ Razor composants](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="0b6e3-115">Un filtre peut uniquement affecter indirectement un composant lorsque :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="0b6e3-116">Le composant est incorporé dans une page ou une vue.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="0b6e3-117">La page ou le contrôleur/la vue utilise le filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="0b6e3-118">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="0b6e3-119">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-119">How filters work</span></span>

<span data-ttu-id="0b6e3-120">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="0b6e3-121">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La demande est traitée par un autre intergiciel, un intergiciel (middleware) de routage, une sélection d’action et le pipeline d’appel d’action.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="0b6e3-124">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-124">Filter types</span></span>

<span data-ttu-id="0b6e3-125">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="0b6e3-126">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="0b6e3-127">Filtres d’autorisation court-circuiter le pipeline si la demande n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="0b6e3-128">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="0b6e3-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="0b6e3-129">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-129">Run after authorization.</span></span>  
  * <span data-ttu-id="0b6e3-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>exécute du code avant le reste du pipeline de filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="0b6e3-131">Par exemple, `OnResourceExecuting` exécute le code avant la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="0b6e3-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>exécute du code une fois que le reste du pipeline est terminé.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="0b6e3-133">[Filtres d’action](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="0b6e3-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="0b6e3-134">Exécutez le code juste avant et après l’appel d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="0b6e3-135">Peut modifier les arguments passés dans une action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="0b6e3-136">Peut modifier le résultat retourné par l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="0b6e3-137">Ne sont **pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="0b6e3-138">Les [filtres d’exception](#exception-filters) appliquent des stratégies globales aux exceptions non gérées qui se produisent avant l’écriture du corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="0b6e3-139">Les [filtres de résultats](#result-filters) exécutent le code immédiatement avant et après l’exécution des résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="0b6e3-140">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="0b6e3-141">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="0b6e3-142">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="0b6e3-145">Implémentation</span><span class="sxs-lookup"><span data-stu-id="0b6e3-145">Implementation</span></span>

<span data-ttu-id="0b6e3-146">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="0b6e3-147">Les filtres synchrones exécutent le code avant et après leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="0b6e3-148">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="0b6e3-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b6e3-150">Les filtres asynchrones définissent une `On-Stage-ExecutionAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="0b6e3-151">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="0b6e3-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="0b6e3-152">Dans le code précédent, `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="0b6e3-153">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-153">Multiple filter stages</span></span>

<span data-ttu-id="0b6e3-154">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="0b6e3-155">Par exemple, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implémente :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="0b6e3-156">Synchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> et<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b6e3-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="0b6e3-157">Asynchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> et<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b6e3-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="0b6e3-158">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="0b6e3-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="0b6e3-159">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="0b6e3-160">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="0b6e3-161">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="0b6e3-162">Lorsque vous utilisez des classes abstraites telles que <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , remplacez uniquement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="0b6e3-163">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="0b6e3-163">Built-in filter attributes</span></span>

<span data-ttu-id="0b6e3-164">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="0b6e3-165">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="0b6e3-166">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="0b6e3-167">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="0b6e3-168">Utilisez un outil tel que les [outils de développement du navigateur](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) pour examiner les en-têtes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="0b6e3-169">Sous **en-têtes de réponse**, `author: Rick Anderson` est affiché.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="0b6e3-170">Le code suivant implémente un `ActionFilterAttribute` qui :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="0b6e3-171">Lit le titre et le nom à partir du système de configuration.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="0b6e3-172">Contrairement à l’exemple précédent, le code suivant ne nécessite pas l’ajout de paramètres de filtre au code.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="0b6e3-173">Ajoute le titre et le nom à l’en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="0b6e3-174">Les options de configuration sont fournies par le [système de configuration](xref:fundamentals/configuration/index) à l’aide du modèle d' [options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0b6e3-175">Par exemple, à partir de l' *appsettings.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-175">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="0b6e3-176">Dans le `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="0b6e3-177">La `PositionOptions` classe est ajoutée au conteneur de services avec la `"Position"` zone de configuration.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="0b6e3-178">Le `MyActionFilterAttribute` est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="0b6e3-179">Le code suivant illustre la `PositionOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="0b6e3-180">Le code suivant applique `MyActionFilterAttribute` à la `Index2` méthode :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="0b6e3-181">Sous **les en-têtes de réponse**, `author: Rick Anderson` et `Editor: Joe Smith` s’affiche lorsque le `Sample/Index2` point de terminaison est appelé.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="0b6e3-182">Le code suivant applique le `MyActionFilterAttribute` et le `AddHeaderAttribute` à la Razor page :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0b6e3-183">Les filtres ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="0b6e3-184">Ils peuvent être appliqués au modèle de Razor page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="0b6e3-185">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="0b6e3-186">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="0b6e3-187">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="0b6e3-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="0b6e3-188">Un filtre peut être ajouté au pipeline à l’une des trois *étendues*suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="0b6e3-189">Utilisation d’un attribut sur une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="0b6e3-190">Les attributs de filtre ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de pages.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="0b6e3-191">Utilisation d’un attribut sur un contrôleur ou une Razor page.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="0b6e3-192">Globalement pour tous les contrôleurs, actions et Razor pages, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="0b6e3-193">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="0b6e3-193">Default order of execution</span></span>

<span data-ttu-id="0b6e3-194">Quand il existe plusieurs filtres pour une étape particulière du pipeline, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="0b6e3-195">Les filtres globaux entourent les filtres de classe, qui à leur tour entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="0b6e3-196">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="0b6e3-197">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-197">The filter sequence:</span></span>

* <span data-ttu-id="0b6e3-198">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="0b6e3-199">*Avant* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="0b6e3-200">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="0b6e3-201">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="0b6e3-202">*Après* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="0b6e3-203">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="0b6e3-204">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="0b6e3-205">Séquence</span><span class="sxs-lookup"><span data-stu-id="0b6e3-205">Sequence</span></span> | <span data-ttu-id="0b6e3-206">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="0b6e3-206">Filter scope</span></span> | <span data-ttu-id="0b6e3-207">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="0b6e3-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="0b6e3-208">1</span><span class="sxs-lookup"><span data-stu-id="0b6e3-208">1</span></span> | <span data-ttu-id="0b6e3-209">Mondiale</span><span class="sxs-lookup"><span data-stu-id="0b6e3-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-210">2</span><span class="sxs-lookup"><span data-stu-id="0b6e3-210">2</span></span> | <span data-ttu-id="0b6e3-211">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="0b6e3-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="0b6e3-212">3</span><span class="sxs-lookup"><span data-stu-id="0b6e3-212">3</span></span> | <span data-ttu-id="0b6e3-213">Méthode</span><span class="sxs-lookup"><span data-stu-id="0b6e3-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-214">4</span><span class="sxs-lookup"><span data-stu-id="0b6e3-214">4</span></span> | <span data-ttu-id="0b6e3-215">Méthode</span><span class="sxs-lookup"><span data-stu-id="0b6e3-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b6e3-216">5</span><span class="sxs-lookup"><span data-stu-id="0b6e3-216">5</span></span> | <span data-ttu-id="0b6e3-217">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="0b6e3-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b6e3-218">6</span><span class="sxs-lookup"><span data-stu-id="0b6e3-218">6</span></span> | <span data-ttu-id="0b6e3-219">Mondiale</span><span class="sxs-lookup"><span data-stu-id="0b6e3-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="0b6e3-220">Filtres au niveau du contrôleur</span><span class="sxs-lookup"><span data-stu-id="0b6e3-220">Controller level filters</span></span>

<span data-ttu-id="0b6e3-221">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="0b6e3-222">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-222">These methods:</span></span>

* <span data-ttu-id="0b6e3-223">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="0b6e3-224">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="0b6e3-225">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="0b6e3-226">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="0b6e3-227">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="0b6e3-228">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="0b6e3-229">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-229">The `TestController`:</span></span>

* <span data-ttu-id="0b6e3-230">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="0b6e3-231">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="0b6e3-232">La navigation vers `https://localhost:5001/Test2/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="0b6e3-233">Les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="0b6e3-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="0b6e3-234">Les filtres au niveau du contrôleur **ne peuvent pas** être configurés pour s’exécuter après les filtres appliqués aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="0b6e3-235">L’ordre est expliqué dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-235">Order is explained in the next section.</span></span>

<span data-ttu-id="0b6e3-236">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="0b6e3-237">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="0b6e3-237">Overriding the default order</span></span>

<span data-ttu-id="0b6e3-238">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="0b6e3-239">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="0b6e3-240">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="0b6e3-241">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="0b6e3-242">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="0b6e3-243">La `Order` propriété est définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="0b6e3-244">Examinez les deux filtres d’action dans le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="0b6e3-245">Un filtre global est ajouté dans `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="0b6e3-246">Les 3 filtres s’exécutent dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="0b6e3-247">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="0b6e3-248">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="0b6e3-249">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="0b6e3-250">Comme mentionné précédemment, les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` pour les filtres intégrés, Scope détermine l’ordre, à moins que `Order` ne soit défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="0b6e3-251">Dans le code précédent, `MySampleActionFilter` a une portée globale pour qu’elle s’exécute avant `MyAction2FilterAttribute` , qui a une portée de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="0b6e3-252">Pour effectuer `MyAction2FilterAttribute` l’exécution en premier, définissez l’ordre sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="0b6e3-253">Pour que le filtre global `MySampleActionFilter` s’exécute en premier, définissez `Order` sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="0b6e3-254">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="0b6e3-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="0b6e3-255">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="0b6e3-256">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="0b6e3-257">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="0b6e3-258">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="0b6e3-259">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="0b6e3-260">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="0b6e3-261">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="0b6e3-262">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="0b6e3-263">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="0b6e3-264">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="0b6e3-264">Dependency injection</span></span>

<span data-ttu-id="0b6e3-265">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="0b6e3-266">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="0b6e3-267">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="0b6e3-268">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-268">A type-activated filter means:</span></span>

* <span data-ttu-id="0b6e3-269">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-269">An instance is created for each request.</span></span>
* <span data-ttu-id="0b6e3-270">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="0b6e3-271">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="0b6e3-272">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="0b6e3-273">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="0b6e3-274">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="0b6e3-275">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="0b6e3-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="0b6e3-277">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="0b6e3-278">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-278">Loggers are available from DI.</span></span> <span data-ttu-id="0b6e3-279">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="0b6e3-280">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="0b6e3-281">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-281">Logging added to filters:</span></span>

* <span data-ttu-id="0b6e3-282">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="0b6e3-283">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="0b6e3-284">Les filtres intégrés consignent les actions et les événements du Framework.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="0b6e3-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b6e3-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="0b6e3-286">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="0b6e3-287">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="0b6e3-288">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b6e3-289">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="0b6e3-290">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="0b6e3-291">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="0b6e3-292">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b6e3-293">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="0b6e3-294">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="0b6e3-295">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="0b6e3-296">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="0b6e3-297">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b6e3-298">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b6e3-299">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="0b6e3-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b6e3-300">TypeFilterAttribute</span></span>

<span data-ttu-id="0b6e3-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="0b6e3-302">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="0b6e3-303">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="0b6e3-304">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="0b6e3-305">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="0b6e3-306">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="0b6e3-307">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="0b6e3-308">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b6e3-309">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="0b6e3-310">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="0b6e3-311">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="0b6e3-312">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="0b6e3-312">Authorization filters</span></span>

<span data-ttu-id="0b6e3-313">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-313">Authorization filters:</span></span>

* <span data-ttu-id="0b6e3-314">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="0b6e3-315">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-315">Control access to action methods.</span></span>
* <span data-ttu-id="0b6e3-316">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="0b6e3-317">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="0b6e3-318">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="0b6e3-319">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="0b6e3-320">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-320">Calls the authorization system.</span></span>
* <span data-ttu-id="0b6e3-321">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-321">Does not authorize requests.</span></span>

<span data-ttu-id="0b6e3-322">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="0b6e3-323">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-323">The exception will not be handled.</span></span>
* <span data-ttu-id="0b6e3-324">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="0b6e3-325">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="0b6e3-326">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="0b6e3-327">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="0b6e3-327">Resource filters</span></span>

<span data-ttu-id="0b6e3-328">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-328">Resource filters:</span></span>

* <span data-ttu-id="0b6e3-329">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="0b6e3-330">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="0b6e3-331">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="0b6e3-332">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="0b6e3-333">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="0b6e3-334">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-334">Resource filter examples:</span></span>

* <span data-ttu-id="0b6e3-335">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="0b6e3-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="0b6e3-337">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="0b6e3-338">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="0b6e3-339">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="0b6e3-339">Action filters</span></span>

<span data-ttu-id="0b6e3-340">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="0b6e3-341">Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="0b6e3-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="0b6e3-342">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="0b6e3-343">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-343">Action filters:</span></span>

* <span data-ttu-id="0b6e3-344">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="0b6e3-345">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="0b6e3-346">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b6e3-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="0b6e3-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-active la lecture des entrées dans une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="0b6e3-349"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="0b6e3-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="0b6e3-351">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="0b6e3-352">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="0b6e3-353">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b6e3-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="0b6e3-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b6e3-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="0b6e3-357">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-357">Setting this property to null:</span></span>

  * <span data-ttu-id="0b6e3-358">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="0b6e3-359">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="0b6e3-360">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="0b6e3-361">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="0b6e3-362">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="0b6e3-363">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="0b6e3-364">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="0b6e3-365">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="0b6e3-366">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-366">Validate model state.</span></span>
* <span data-ttu-id="0b6e3-367">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="0b6e3-368">Les contrôleurs annotés avec l' `[ApiController]` attribut valident automatiquement l’état du modèle et retournent une réponse 400.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="0b6e3-369">Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="0b6e3-370">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="0b6e3-371">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="0b6e3-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b6e3-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="0b6e3-374">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="0b6e3-375">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="0b6e3-376">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="0b6e3-377">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="0b6e3-377">Exception filters</span></span>

<span data-ttu-id="0b6e3-378">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-378">Exception filters:</span></span>

* <span data-ttu-id="0b6e3-379">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="0b6e3-380">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="0b6e3-381">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="0b6e3-382">Le code suivant teste le filtre d’exception :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="0b6e3-383">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-383">Exception filters:</span></span>

* <span data-ttu-id="0b6e3-384">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-384">Don't have before and after events.</span></span>
* <span data-ttu-id="0b6e3-385">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="0b6e3-386">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="0b6e3-387">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="0b6e3-388">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="0b6e3-389">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-389">This stops propagation of the exception.</span></span> <span data-ttu-id="0b6e3-390">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="0b6e3-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="0b6e3-391">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-391">Only an action filter can do that.</span></span>

<span data-ttu-id="0b6e3-392">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-392">Exception filters:</span></span>

* <span data-ttu-id="0b6e3-393">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="0b6e3-394">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="0b6e3-395">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="0b6e3-396">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="0b6e3-397">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="0b6e3-398">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="0b6e3-399">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="0b6e3-399">Result filters</span></span>

<span data-ttu-id="0b6e3-400">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-400">Result filters:</span></span>

* <span data-ttu-id="0b6e3-401">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-401">Implement an interface:</span></span>
  * <span data-ttu-id="0b6e3-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b6e3-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="0b6e3-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b6e3-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="0b6e3-404">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="0b6e3-405">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b6e3-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="0b6e3-406">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b6e3-407">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="0b6e3-408">Une action qui retourne une vue comprend tout le traitement Razor dans le cadre du <xref:Microsoft.AspNetCore.Mvc.ViewResult> en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="0b6e3-409">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="0b6e3-410">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="0b6e3-411">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="0b6e3-412">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="0b6e3-413">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="0b6e3-414">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="0b6e3-415">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="0b6e3-416">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="0b6e3-417">Levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="0b6e3-418">Empêche l’exécution du résultat de l’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="0b6e3-419">Est traité comme un échec au lieu d’un résultat réussi.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b6e3-420">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="0b6e3-421">Si la réponse a déjà été envoyée au client, elle ne peut pas être modifiée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="0b6e3-422">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="0b6e3-423">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="0b6e3-424">La définition `Exception` de la valeur null gère efficacement une exception et empêche la levée de l’exception plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="0b6e3-425">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="0b6e3-426">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="0b6e3-427">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="0b6e3-428">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="0b6e3-429">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="0b6e3-430">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="0b6e3-431">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b6e3-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="0b6e3-432">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="0b6e3-433">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-433">This includes action results produced by:</span></span>

* <span data-ttu-id="0b6e3-434">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="0b6e3-435">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-435">Exception filters.</span></span>

<span data-ttu-id="0b6e3-436">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="0b6e3-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="0b6e3-437">IFilterFactory</span></span>

<span data-ttu-id="0b6e3-438">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="0b6e3-439">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="0b6e3-440">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="0b6e3-441">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="0b6e3-442">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="0b6e3-443">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="0b6e3-444">Le filtre est appliqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="0b6e3-445">Testez le code précédent en exécutant l' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="0b6e3-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="0b6e3-446">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="0b6e3-447">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="0b6e3-448">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="0b6e3-449">**Auteur :**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="0b6e3-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="0b6e3-450">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="0b6e3-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="0b6e3-451">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="0b6e3-451">**internal:** `My header`</span></span>

<span data-ttu-id="0b6e3-452">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="0b6e3-453">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="0b6e3-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="0b6e3-454">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="0b6e3-455">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="0b6e3-456">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="0b6e3-457">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b6e3-458">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b6e3-459">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="0b6e3-460">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="0b6e3-461">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="0b6e3-462">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="0b6e3-463">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="0b6e3-464">Toutefois, les filtres diffèrent des intergiciels (middleware) en ce sens qu’ils font partie du runtime, ce qui signifie qu’ils ont accès au contexte et aux constructions.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="0b6e3-465">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="0b6e3-466">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="0b6e3-467">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="0b6e3-468">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="0b6e3-469">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="0b6e3-469">Next actions</span></span>

* <span data-ttu-id="0b6e3-470">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="0b6e3-471">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b6e3-472">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0b6e3-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0b6e3-473">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="0b6e3-474">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="0b6e3-475">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="0b6e3-476">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="0b6e3-477">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="0b6e3-478">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="0b6e3-479">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="0b6e3-480">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="0b6e3-481">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="0b6e3-482">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="0b6e3-483">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-483">How filters work</span></span>

<span data-ttu-id="0b6e3-484">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="0b6e3-485">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La requête est traitée via un autre intergiciel, un intergiciel de routage, une sélection d’action et le pipeline d’appels d’action ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="0b6e3-488">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-488">Filter types</span></span>

<span data-ttu-id="0b6e3-489">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="0b6e3-490">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="0b6e3-491">Les filtres d’autorisations peuvent court-circuiter le pipeline si la requête n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="0b6e3-492">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="0b6e3-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="0b6e3-493">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-493">Run after authorization.</span></span>  
  * <span data-ttu-id="0b6e3-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> peut exécuter du code avant le reste du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="0b6e3-495">Par exemple, `OnResourceExecuting` peut exécuter du code avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="0b6e3-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> peut exécuter du code une fois que le reste du pipeline terminé.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="0b6e3-497">Les [filtres d’actions](#action-filters) peuvent exécuter du code juste avant et juste après l’appel d’une méthode d’action individuelle.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="0b6e3-498">Ils peuvent être utilisés pour manipuler les arguments passés à une action et le résultat retourné depuis l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="0b6e3-499">Les filtres d’action **ne sont pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="0b6e3-500">Les [filtres d’exceptions](#exception-filters) sont utilisés pour appliquer des stratégies globales à des exceptions non gérées qui se produisent avant que des éléments aient été écrits dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="0b6e3-501">Les [filtres de résultats](#result-filters) peuvent exécuter du code juste avant et juste après l’exécution des résultats d’une action individuelle.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="0b6e3-502">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="0b6e3-503">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="0b6e3-504">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="0b6e3-507">Implémentation</span><span class="sxs-lookup"><span data-stu-id="0b6e3-507">Implementation</span></span>

<span data-ttu-id="0b6e3-508">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="0b6e3-509">Les filtres synchrones peuvent exécuter du code avant (`On-Stage-Executing`) et après (`On-Stage-Executed`) leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="0b6e3-510">Par exemple, `OnActionExecuting` est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="0b6e3-511">`OnActionExecuted` est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b6e3-512">Les filtres asynchrones définissent une méthode `On-Stage-ExecutionAsync` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="0b6e3-513">Dans le code précédent, le `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="0b6e3-514">Chacune des méthodes `On-Stage-ExecutionAsync` prennent un `FilterType-ExecutionDelegate` qui exécute l’étape de pipeline du filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="0b6e3-515">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-515">Multiple filter stages</span></span>

<span data-ttu-id="0b6e3-516">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="0b6e3-517">Par exemple, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implémente `IActionFilter`, `IResultFilter` et leurs équivalents asynchrones.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="0b6e3-518">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="0b6e3-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="0b6e3-519">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="0b6e3-520">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="0b6e3-521">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="0b6e3-522">Quand vous utilisez des classes abstraites comme <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, vous devez remplacer seulement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="0b6e3-523">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="0b6e3-523">Built-in filter attributes</span></span>

<span data-ttu-id="0b6e3-524">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="0b6e3-525">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="0b6e3-526">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="0b6e3-527">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="0b6e3-528">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="0b6e3-529">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="0b6e3-530">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="0b6e3-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="0b6e3-531">Un filtre peut être ajouté au pipeline à l’une des trois *étendues*suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="0b6e3-532">À l’aide d’un attribut sur une action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="0b6e3-533">À l’aide d’un attribut sur un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="0b6e3-534">Dans le monde entier pour tous les contrôleurs et actions, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0b6e3-535">Le code précédent ajoute trois filtres globalement à l’aide de la collection [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="0b6e3-536">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="0b6e3-536">Default order of execution</span></span>

<span data-ttu-id="0b6e3-537">Lorsqu’il existe plusieurs filtres *du même type*, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="0b6e3-538">Filtres globaux-filtres de classe surround.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-538">Global filters surround class filters.</span></span> <span data-ttu-id="0b6e3-539">Les filtres de classe entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-539">Class filters surround method filters.</span></span>

<span data-ttu-id="0b6e3-540">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="0b6e3-541">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-541">The filter sequence:</span></span>

* <span data-ttu-id="0b6e3-542">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="0b6e3-543">Le code *avant* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="0b6e3-544">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="0b6e3-545">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="0b6e3-546">Le code *après* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="0b6e3-547">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="0b6e3-548">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="0b6e3-549">Séquence</span><span class="sxs-lookup"><span data-stu-id="0b6e3-549">Sequence</span></span> | <span data-ttu-id="0b6e3-550">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="0b6e3-550">Filter scope</span></span> | <span data-ttu-id="0b6e3-551">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="0b6e3-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="0b6e3-552">1</span><span class="sxs-lookup"><span data-stu-id="0b6e3-552">1</span></span> | <span data-ttu-id="0b6e3-553">Mondiale</span><span class="sxs-lookup"><span data-stu-id="0b6e3-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-554">2</span><span class="sxs-lookup"><span data-stu-id="0b6e3-554">2</span></span> | <span data-ttu-id="0b6e3-555">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="0b6e3-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-556">3</span><span class="sxs-lookup"><span data-stu-id="0b6e3-556">3</span></span> | <span data-ttu-id="0b6e3-557">Méthode</span><span class="sxs-lookup"><span data-stu-id="0b6e3-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-558">4</span><span class="sxs-lookup"><span data-stu-id="0b6e3-558">4</span></span> | <span data-ttu-id="0b6e3-559">Méthode</span><span class="sxs-lookup"><span data-stu-id="0b6e3-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b6e3-560">5</span><span class="sxs-lookup"><span data-stu-id="0b6e3-560">5</span></span> | <span data-ttu-id="0b6e3-561">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="0b6e3-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b6e3-562">6</span><span class="sxs-lookup"><span data-stu-id="0b6e3-562">6</span></span> | <span data-ttu-id="0b6e3-563">Mondiale</span><span class="sxs-lookup"><span data-stu-id="0b6e3-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="0b6e3-564">Cette séquence montre que :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-564">This sequence shows:</span></span>

* <span data-ttu-id="0b6e3-565">Le filtre de méthode est imbriqué dans le filtre de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="0b6e3-566">Le filtre de contrôleur est imbriqué dans le filtre global.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="0b6e3-567">Filtres au niveau du contrôleur et de la Razor page</span><span class="sxs-lookup"><span data-stu-id="0b6e3-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="0b6e3-568">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="0b6e3-569">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-569">These methods:</span></span>

* <span data-ttu-id="0b6e3-570">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="0b6e3-571">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="0b6e3-572">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="0b6e3-573">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="0b6e3-574">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="0b6e3-575">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="0b6e3-576">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-576">The `TestController`:</span></span>

* <span data-ttu-id="0b6e3-577">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="0b6e3-578">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="0b6e3-579">La navigation vers `https://localhost:5001/Test/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="0b6e3-580">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="0b6e3-581">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="0b6e3-581">Overriding the default order</span></span>

<span data-ttu-id="0b6e3-582">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="0b6e3-583">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="0b6e3-584">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="0b6e3-585">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="0b6e3-586">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="0b6e3-587">La propriété `Order` peut être définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="0b6e3-588">Prenez en compte les mêmes 3 filtres d’actions indiqués dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="0b6e3-589">Si la propriété `Order` du contrôleur et les filtres globaux sont définis sur 1 et 2 respectivement, l’ordre d’exécution est inversé.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="0b6e3-590">Séquence</span><span class="sxs-lookup"><span data-stu-id="0b6e3-590">Sequence</span></span> | <span data-ttu-id="0b6e3-591">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="0b6e3-591">Filter scope</span></span> | <span data-ttu-id="0b6e3-592">Propriété `Order`</span><span class="sxs-lookup"><span data-stu-id="0b6e3-592">`Order` property</span></span> | <span data-ttu-id="0b6e3-593">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="0b6e3-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="0b6e3-594">1</span><span class="sxs-lookup"><span data-stu-id="0b6e3-594">1</span></span> | <span data-ttu-id="0b6e3-595">Méthode</span><span class="sxs-lookup"><span data-stu-id="0b6e3-595">Method</span></span> | <span data-ttu-id="0b6e3-596">0</span><span class="sxs-lookup"><span data-stu-id="0b6e3-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-597">2</span><span class="sxs-lookup"><span data-stu-id="0b6e3-597">2</span></span> | <span data-ttu-id="0b6e3-598">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="0b6e3-598">Controller</span></span> | <span data-ttu-id="0b6e3-599">1</span><span class="sxs-lookup"><span data-stu-id="0b6e3-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-600">3</span><span class="sxs-lookup"><span data-stu-id="0b6e3-600">3</span></span> | <span data-ttu-id="0b6e3-601">Mondiale</span><span class="sxs-lookup"><span data-stu-id="0b6e3-601">Global</span></span> | <span data-ttu-id="0b6e3-602">2</span><span class="sxs-lookup"><span data-stu-id="0b6e3-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="0b6e3-603">4</span><span class="sxs-lookup"><span data-stu-id="0b6e3-603">4</span></span> | <span data-ttu-id="0b6e3-604">Mondiale</span><span class="sxs-lookup"><span data-stu-id="0b6e3-604">Global</span></span> | <span data-ttu-id="0b6e3-605">2</span><span class="sxs-lookup"><span data-stu-id="0b6e3-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="0b6e3-606">5</span><span class="sxs-lookup"><span data-stu-id="0b6e3-606">5</span></span> | <span data-ttu-id="0b6e3-607">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="0b6e3-607">Controller</span></span> | <span data-ttu-id="0b6e3-608">1</span><span class="sxs-lookup"><span data-stu-id="0b6e3-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="0b6e3-609">6</span><span class="sxs-lookup"><span data-stu-id="0b6e3-609">6</span></span> | <span data-ttu-id="0b6e3-610">Méthode</span><span class="sxs-lookup"><span data-stu-id="0b6e3-610">Method</span></span> | <span data-ttu-id="0b6e3-611">0</span><span class="sxs-lookup"><span data-stu-id="0b6e3-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="0b6e3-612">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="0b6e3-613">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="0b6e3-614">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="0b6e3-615">Pour les filtres intégrés, l’étendue détermine l’ordre, sauf si `Order` est défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="0b6e3-616">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="0b6e3-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="0b6e3-617">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="0b6e3-618">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="0b6e3-619">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="0b6e3-620">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="0b6e3-621">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="0b6e3-622">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="0b6e3-623">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="0b6e3-624">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="0b6e3-625">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="0b6e3-626">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="0b6e3-626">Dependency injection</span></span>

<span data-ttu-id="0b6e3-627">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="0b6e3-628">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="0b6e3-629">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="0b6e3-630">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-630">A type-activated filter means:</span></span>

* <span data-ttu-id="0b6e3-631">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-631">An instance is created for each request.</span></span>
* <span data-ttu-id="0b6e3-632">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="0b6e3-633">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="0b6e3-634">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="0b6e3-635">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="0b6e3-636">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="0b6e3-637">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="0b6e3-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="0b6e3-639">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="0b6e3-640">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-640">Loggers are available from DI.</span></span> <span data-ttu-id="0b6e3-641">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="0b6e3-642">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="0b6e3-643">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-643">Logging added to filters:</span></span>

* <span data-ttu-id="0b6e3-644">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="0b6e3-645">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="0b6e3-646">Les filtres intégrés enregistrent des actions et des événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="0b6e3-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b6e3-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="0b6e3-648">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="0b6e3-649">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="0b6e3-650">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b6e3-651">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="0b6e3-652">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="0b6e3-653">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="0b6e3-654">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b6e3-655">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="0b6e3-656">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="0b6e3-657">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="0b6e3-658">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="0b6e3-659">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b6e3-660">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b6e3-661">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="0b6e3-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b6e3-662">TypeFilterAttribute</span></span>

<span data-ttu-id="0b6e3-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="0b6e3-664">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="0b6e3-665">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="0b6e3-666">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="0b6e3-667">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="0b6e3-668">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="0b6e3-669">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="0b6e3-670">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b6e3-671">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="0b6e3-672">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="0b6e3-673">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="0b6e3-674">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="0b6e3-674">Authorization filters</span></span>

<span data-ttu-id="0b6e3-675">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-675">Authorization filters:</span></span>

* <span data-ttu-id="0b6e3-676">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="0b6e3-677">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-677">Control access to action methods.</span></span>
* <span data-ttu-id="0b6e3-678">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="0b6e3-679">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="0b6e3-680">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="0b6e3-681">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="0b6e3-682">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-682">Calls the authorization system.</span></span>
* <span data-ttu-id="0b6e3-683">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-683">Does not authorize requests.</span></span>

<span data-ttu-id="0b6e3-684">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="0b6e3-685">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-685">The exception will not be handled.</span></span>
* <span data-ttu-id="0b6e3-686">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="0b6e3-687">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="0b6e3-688">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="0b6e3-689">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="0b6e3-689">Resource filters</span></span>

<span data-ttu-id="0b6e3-690">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-690">Resource filters:</span></span>

* <span data-ttu-id="0b6e3-691">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="0b6e3-692">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="0b6e3-693">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="0b6e3-694">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="0b6e3-695">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="0b6e3-696">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-696">Resource filter examples:</span></span>

* <span data-ttu-id="0b6e3-697">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="0b6e3-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="0b6e3-699">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="0b6e3-700">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="0b6e3-701">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="0b6e3-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0b6e3-702">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="0b6e3-703">Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="0b6e3-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="0b6e3-704">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="0b6e3-705">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-705">Action filters:</span></span>

* <span data-ttu-id="0b6e3-706">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="0b6e3-707">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="0b6e3-708">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b6e3-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="0b6e3-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> : permet de lire les entrées vers une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="0b6e3-711"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="0b6e3-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="0b6e3-713">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="0b6e3-714">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="0b6e3-715">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b6e3-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="0b6e3-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b6e3-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="0b6e3-719">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-719">Setting this property to null:</span></span>

  * <span data-ttu-id="0b6e3-720">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="0b6e3-721">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="0b6e3-722">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="0b6e3-723">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="0b6e3-724">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="0b6e3-725">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="0b6e3-726">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="0b6e3-727">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="0b6e3-728">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-728">Validate model state.</span></span>
* <span data-ttu-id="0b6e3-729">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="0b6e3-730">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="0b6e3-731">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="0b6e3-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b6e3-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="0b6e3-734">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="0b6e3-735">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="0b6e3-736">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="0b6e3-737">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="0b6e3-737">Exception filters</span></span>

<span data-ttu-id="0b6e3-738">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-738">Exception filters:</span></span>

* <span data-ttu-id="0b6e3-739">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="0b6e3-740">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="0b6e3-741">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="0b6e3-742">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-742">Exception filters:</span></span>

* <span data-ttu-id="0b6e3-743">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-743">Don't have before and after events.</span></span>
* <span data-ttu-id="0b6e3-744">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="0b6e3-745">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="0b6e3-746">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="0b6e3-747">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="0b6e3-748">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-748">This stops propagation of the exception.</span></span> <span data-ttu-id="0b6e3-749">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="0b6e3-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="0b6e3-750">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-750">Only an action filter can do that.</span></span>

<span data-ttu-id="0b6e3-751">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-751">Exception filters:</span></span>

* <span data-ttu-id="0b6e3-752">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="0b6e3-753">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="0b6e3-754">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="0b6e3-755">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="0b6e3-756">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="0b6e3-757">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="0b6e3-758">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="0b6e3-758">Result filters</span></span>

<span data-ttu-id="0b6e3-759">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-759">Result filters:</span></span>

* <span data-ttu-id="0b6e3-760">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-760">Implement an interface:</span></span>
  * <span data-ttu-id="0b6e3-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b6e3-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="0b6e3-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b6e3-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="0b6e3-763">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="0b6e3-764">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b6e3-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="0b6e3-765">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b6e3-766">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="0b6e3-767">Une action retournant un affichage inclut tous les traitements Razor dans le cadre de la <xref:Microsoft.AspNetCore.Mvc.ViewResult> à exécuter.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="0b6e3-768">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="0b6e3-769">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="0b6e3-770">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="0b6e3-771">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="0b6e3-772">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="0b6e3-773">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="0b6e3-774">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="0b6e3-775">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="0b6e3-776">La levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="0b6e3-777">Empêche l’exécution du résultat d’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="0b6e3-778">Est traitée comme une erreur et non comme une réussite.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b6e3-779">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="0b6e3-780">Si la réponse a déjà été envoyée au client, elle ne peut plus être modifiée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="0b6e3-781">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="0b6e3-782">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="0b6e3-783">Le paramètre de `Exception` sur Null gère effectivement une exception et évite à l’exception d’être à nouveau levée par ASP.NET Core plus loin dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="0b6e3-784">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="0b6e3-785">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="0b6e3-786">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="0b6e3-787">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="0b6e3-788">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="0b6e3-789">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="0b6e3-790">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b6e3-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="0b6e3-791">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="0b6e3-792">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-792">This includes action results produced by:</span></span>

* <span data-ttu-id="0b6e3-793">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="0b6e3-794">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-794">Exception filters.</span></span>

<span data-ttu-id="0b6e3-795">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="0b6e3-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="0b6e3-796">IFilterFactory</span></span>

<span data-ttu-id="0b6e3-797">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="0b6e3-798">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="0b6e3-799">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="0b6e3-800">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="0b6e3-801">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="0b6e3-802">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="0b6e3-803">Le code précédent peut être testé en exécutant l’[échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="0b6e3-804">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="0b6e3-805">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="0b6e3-806">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="0b6e3-807">**Auteur :**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="0b6e3-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="0b6e3-808">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="0b6e3-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="0b6e3-809">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="0b6e3-809">**internal:** `My header`</span></span>

<span data-ttu-id="0b6e3-810">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="0b6e3-811">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="0b6e3-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="0b6e3-812">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="0b6e3-813">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="0b6e3-814">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="0b6e3-815">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b6e3-816">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b6e3-817">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="0b6e3-818">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="0b6e3-819">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="0b6e3-820">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="0b6e3-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="0b6e3-821">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="0b6e3-822">Les filtres diffèrent cependant d’un intergiciel dans la mesure où ils font partie du runtime ASP.NET Core, ce qui signifie qu’ils ont accès au contexte et aux constructions ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="0b6e3-823">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="0b6e3-824">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="0b6e3-825">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="0b6e3-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="0b6e3-826">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="0b6e3-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="0b6e3-827">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="0b6e3-827">Next actions</span></span>

* <span data-ttu-id="0b6e3-828">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="0b6e3-829">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="0b6e3-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
