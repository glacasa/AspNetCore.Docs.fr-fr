---
title: Filtres dans ASP.NET Core
author: Rick-Anderson
description: Découvrez comment les filtres fonctionnent et comment les utiliser dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 407583533939ec1077af8e1a1511ed187ef9de69
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103007"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="4ca0c-103">Filtres dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ca0c-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ca0c-104">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4ca0c-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4ca0c-105">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="4ca0c-106">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="4ca0c-107">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="4ca0c-108">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="4ca0c-109">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="4ca0c-110">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="4ca0c-111">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="4ca0c-112">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="4ca0c-113">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="4ca0c-114">Les filtres ne fonctionnent pas directement avec les [ Razor composants](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="4ca0c-115">Un filtre peut uniquement affecter indirectement un composant lorsque :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="4ca0c-116">Le composant est incorporé dans une page ou une vue.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="4ca0c-117">La page ou le contrôleur/la vue utilise le filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="4ca0c-118">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="4ca0c-119">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-119">How filters work</span></span>

<span data-ttu-id="4ca0c-120">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="4ca0c-121">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La demande est traitée par un autre intergiciel, un intergiciel (middleware) de routage, une sélection d’action et le pipeline d’appel d’action.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="4ca0c-124">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-124">Filter types</span></span>

<span data-ttu-id="4ca0c-125">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="4ca0c-126">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="4ca0c-127">Filtres d’autorisation court-circuiter le pipeline si la demande n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="4ca0c-128">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="4ca0c-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="4ca0c-129">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-129">Run after authorization.</span></span>  
  * <span data-ttu-id="4ca0c-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>exécute du code avant le reste du pipeline de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="4ca0c-131">Par exemple, `OnResourceExecuting` exécute le code avant la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="4ca0c-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>exécute du code une fois que le reste du pipeline est terminé.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="4ca0c-133">[Filtres d’action](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="4ca0c-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="4ca0c-134">Exécutez le code juste avant et après l’appel d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="4ca0c-135">Peut modifier les arguments passés dans une action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="4ca0c-136">Peut modifier le résultat retourné par l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="4ca0c-137">Ne sont **pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="4ca0c-138">Les [filtres d’exception](#exception-filters) appliquent des stratégies globales aux exceptions non gérées qui se produisent avant l’écriture du corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="4ca0c-139">Les [filtres de résultats](#result-filters) exécutent le code immédiatement avant et après l’exécution des résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="4ca0c-140">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="4ca0c-141">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="4ca0c-142">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="4ca0c-145">Implémentation</span><span class="sxs-lookup"><span data-stu-id="4ca0c-145">Implementation</span></span>

<span data-ttu-id="4ca0c-146">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="4ca0c-147">Les filtres synchrones exécutent le code avant et après leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="4ca0c-148">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="4ca0c-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ca0c-150">Les filtres asynchrones définissent une `On-Stage-ExecutionAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="4ca0c-151">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="4ca0c-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="4ca0c-152">Dans le code précédent, `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="4ca0c-153">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-153">Multiple filter stages</span></span>

<span data-ttu-id="4ca0c-154">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="4ca0c-155">Par exemple, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implémente :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="4ca0c-156">Synchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> et<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ca0c-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="4ca0c-157">Asynchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> et<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ca0c-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="4ca0c-158">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="4ca0c-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="4ca0c-159">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4ca0c-160">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4ca0c-161">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="4ca0c-162">Lorsque vous utilisez des classes abstraites telles que <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , remplacez uniquement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="4ca0c-163">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="4ca0c-163">Built-in filter attributes</span></span>

<span data-ttu-id="4ca0c-164">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="4ca0c-165">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="4ca0c-166">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="4ca0c-167">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="4ca0c-168">Utilisez un outil tel que les [outils de développement du navigateur](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) pour examiner les en-têtes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="4ca0c-169">Sous **en-têtes de réponse**, `author: Rick Anderson` est affiché.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="4ca0c-170">Le code suivant implémente un `ActionFilterAttribute` qui :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="4ca0c-171">Lit le titre et le nom à partir du système de configuration.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="4ca0c-172">Contrairement à l’exemple précédent, le code suivant ne nécessite pas l’ajout de paramètres de filtre au code.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="4ca0c-173">Ajoute le titre et le nom à l’en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4ca0c-174">Les options de configuration sont fournies par le [système de configuration](xref:fundamentals/configuration/index) à l’aide du modèle d' [options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4ca0c-175">Par exemple, à partir de l' *appsettings.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="4ca0c-176">Dans le `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="4ca0c-177">La `PositionOptions` classe est ajoutée au conteneur de services avec la `"Position"` zone de configuration.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="4ca0c-178">Le `MyActionFilterAttribute` est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="4ca0c-179">Le code suivant illustre la `PositionOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="4ca0c-180">Le code suivant applique `MyActionFilterAttribute` à la `Index2` méthode :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="4ca0c-181">Sous **les en-têtes de réponse**, `author: Rick Anderson` et `Editor: Joe Smith` s’affiche lorsque le `Sample/Index2` point de terminaison est appelé.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="4ca0c-182">Le code suivant applique le `MyActionFilterAttribute` et le `AddHeaderAttribute` à la Razor page :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4ca0c-183">Les filtres ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="4ca0c-184">Ils peuvent être appliqués au modèle de Razor page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="4ca0c-185">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="4ca0c-186">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="4ca0c-187">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="4ca0c-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="4ca0c-188">Un filtre peut être ajouté au pipeline à l’une des trois *étendues*suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="4ca0c-189">Utilisation d’un attribut sur une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="4ca0c-190">Les attributs de filtre ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de pages.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="4ca0c-191">Utilisation d’un attribut sur un contrôleur ou une Razor page.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="4ca0c-192">Globalement pour tous les contrôleurs, actions et Razor pages, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="4ca0c-193">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="4ca0c-193">Default order of execution</span></span>

<span data-ttu-id="4ca0c-194">Quand il existe plusieurs filtres pour une étape particulière du pipeline, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="4ca0c-195">Les filtres globaux entourent les filtres de classe, qui à leur tour entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="4ca0c-196">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="4ca0c-197">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-197">The filter sequence:</span></span>

* <span data-ttu-id="4ca0c-198">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="4ca0c-199">*Avant* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="4ca0c-200">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="4ca0c-201">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="4ca0c-202">*Après* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="4ca0c-203">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="4ca0c-204">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="4ca0c-205">Séquence</span><span class="sxs-lookup"><span data-stu-id="4ca0c-205">Sequence</span></span> | <span data-ttu-id="4ca0c-206">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="4ca0c-206">Filter scope</span></span> | <span data-ttu-id="4ca0c-207">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="4ca0c-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="4ca0c-208">1</span><span class="sxs-lookup"><span data-stu-id="4ca0c-208">1</span></span> | <span data-ttu-id="4ca0c-209">Globale</span><span class="sxs-lookup"><span data-stu-id="4ca0c-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-210">2</span><span class="sxs-lookup"><span data-stu-id="4ca0c-210">2</span></span> | <span data-ttu-id="4ca0c-211">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="4ca0c-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="4ca0c-212">3</span><span class="sxs-lookup"><span data-stu-id="4ca0c-212">3</span></span> | <span data-ttu-id="4ca0c-213">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ca0c-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-214">4</span><span class="sxs-lookup"><span data-stu-id="4ca0c-214">4</span></span> | <span data-ttu-id="4ca0c-215">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ca0c-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ca0c-216">5</span><span class="sxs-lookup"><span data-stu-id="4ca0c-216">5</span></span> | <span data-ttu-id="4ca0c-217">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="4ca0c-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ca0c-218">6</span><span class="sxs-lookup"><span data-stu-id="4ca0c-218">6</span></span> | <span data-ttu-id="4ca0c-219">Globale</span><span class="sxs-lookup"><span data-stu-id="4ca0c-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="4ca0c-220">Filtres au niveau du contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ca0c-220">Controller level filters</span></span>

<span data-ttu-id="4ca0c-221">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="4ca0c-222">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-222">These methods:</span></span>

* <span data-ttu-id="4ca0c-223">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="4ca0c-224">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="4ca0c-225">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="4ca0c-226">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="4ca0c-227">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="4ca0c-228">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="4ca0c-229">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-229">The `TestController`:</span></span>

* <span data-ttu-id="4ca0c-230">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="4ca0c-231">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="4ca0c-232">La navigation vers `https://localhost:5001/Test2/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="4ca0c-233">Les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="4ca0c-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="4ca0c-234">Les filtres au niveau du contrôleur **ne peuvent pas** être configurés pour s’exécuter après les filtres appliqués aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="4ca0c-235">L’ordre est expliqué dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-235">Order is explained in the next section.</span></span>

<span data-ttu-id="4ca0c-236">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="4ca0c-237">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="4ca0c-237">Overriding the default order</span></span>

<span data-ttu-id="4ca0c-238">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="4ca0c-239">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="4ca0c-240">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="4ca0c-241">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="4ca0c-242">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="4ca0c-243">La `Order` propriété est définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="4ca0c-244">Examinez les deux filtres d’action dans le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="4ca0c-245">Un filtre global est ajouté dans `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="4ca0c-246">Les 3 filtres s’exécutent dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="4ca0c-247">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="4ca0c-248">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="4ca0c-249">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="4ca0c-250">Comme mentionné précédemment, les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` pour les filtres intégrés, Scope détermine l’ordre, à moins que `Order` ne soit défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="4ca0c-251">Dans le code précédent, `MySampleActionFilter` a une portée globale pour qu’elle s’exécute avant `MyAction2FilterAttribute` , qui a une portée de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="4ca0c-252">Pour effectuer `MyAction2FilterAttribute` l’exécution en premier, définissez l’ordre sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="4ca0c-253">Pour que le filtre global `MySampleActionFilter` s’exécute en premier, définissez `Order` sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="4ca0c-254">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="4ca0c-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="4ca0c-255">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="4ca0c-256">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4ca0c-257">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="4ca0c-258">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="4ca0c-259">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="4ca0c-260">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="4ca0c-261">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="4ca0c-262">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="4ca0c-263">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="4ca0c-264">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="4ca0c-264">Dependency injection</span></span>

<span data-ttu-id="4ca0c-265">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="4ca0c-266">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="4ca0c-267">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="4ca0c-268">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-268">A type-activated filter means:</span></span>

* <span data-ttu-id="4ca0c-269">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-269">An instance is created for each request.</span></span>
* <span data-ttu-id="4ca0c-270">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="4ca0c-271">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="4ca0c-272">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="4ca0c-273">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="4ca0c-274">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="4ca0c-275">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="4ca0c-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="4ca0c-277">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="4ca0c-278">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-278">Loggers are available from DI.</span></span> <span data-ttu-id="4ca0c-279">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="4ca0c-280">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="4ca0c-281">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-281">Logging added to filters:</span></span>

* <span data-ttu-id="4ca0c-282">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="4ca0c-283">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="4ca0c-284">Les filtres intégrés consignent les actions et les événements du Framework.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="4ca0c-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ca0c-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="4ca0c-286">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="4ca0c-287">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="4ca0c-288">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ca0c-289">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="4ca0c-290">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="4ca0c-291">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="4ca0c-292">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ca0c-293">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="4ca0c-294">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="4ca0c-295">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="4ca0c-296">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="4ca0c-297">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ca0c-298">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ca0c-299">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="4ca0c-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ca0c-300">TypeFilterAttribute</span></span>

<span data-ttu-id="4ca0c-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="4ca0c-302">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="4ca0c-303">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="4ca0c-304">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="4ca0c-305">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="4ca0c-306">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="4ca0c-307">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="4ca0c-308">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ca0c-309">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="4ca0c-310">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="4ca0c-311">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="4ca0c-312">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="4ca0c-312">Authorization filters</span></span>

<span data-ttu-id="4ca0c-313">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-313">Authorization filters:</span></span>

* <span data-ttu-id="4ca0c-314">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="4ca0c-315">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-315">Control access to action methods.</span></span>
* <span data-ttu-id="4ca0c-316">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="4ca0c-317">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="4ca0c-318">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="4ca0c-319">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="4ca0c-320">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-320">Calls the authorization system.</span></span>
* <span data-ttu-id="4ca0c-321">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-321">Does not authorize requests.</span></span>

<span data-ttu-id="4ca0c-322">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="4ca0c-323">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-323">The exception will not be handled.</span></span>
* <span data-ttu-id="4ca0c-324">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="4ca0c-325">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="4ca0c-326">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="4ca0c-327">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="4ca0c-327">Resource filters</span></span>

<span data-ttu-id="4ca0c-328">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-328">Resource filters:</span></span>

* <span data-ttu-id="4ca0c-329">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="4ca0c-330">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="4ca0c-331">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="4ca0c-332">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="4ca0c-333">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="4ca0c-334">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-334">Resource filter examples:</span></span>

* <span data-ttu-id="4ca0c-335">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="4ca0c-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="4ca0c-337">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="4ca0c-338">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="4ca0c-339">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="4ca0c-339">Action filters</span></span>

<span data-ttu-id="4ca0c-340">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="4ca0c-341">Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="4ca0c-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="4ca0c-342">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="4ca0c-343">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-343">Action filters:</span></span>

* <span data-ttu-id="4ca0c-344">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="4ca0c-345">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="4ca0c-346">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ca0c-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="4ca0c-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-active la lecture des entrées dans une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="4ca0c-349"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="4ca0c-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="4ca0c-351">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="4ca0c-352">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="4ca0c-353">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ca0c-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="4ca0c-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ca0c-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="4ca0c-357">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-357">Setting this property to null:</span></span>

  * <span data-ttu-id="4ca0c-358">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="4ca0c-359">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="4ca0c-360">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="4ca0c-361">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="4ca0c-362">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="4ca0c-363">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="4ca0c-364">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="4ca0c-365">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="4ca0c-366">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-366">Validate model state.</span></span>
* <span data-ttu-id="4ca0c-367">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="4ca0c-368">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="4ca0c-369">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="4ca0c-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ca0c-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="4ca0c-372">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="4ca0c-373">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="4ca0c-374">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="4ca0c-375">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="4ca0c-375">Exception filters</span></span>

<span data-ttu-id="4ca0c-376">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-376">Exception filters:</span></span>

* <span data-ttu-id="4ca0c-377">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="4ca0c-378">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="4ca0c-379">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="4ca0c-380">Le code suivant teste le filtre d’exception :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="4ca0c-381">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-381">Exception filters:</span></span>

* <span data-ttu-id="4ca0c-382">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-382">Don't have before and after events.</span></span>
* <span data-ttu-id="4ca0c-383">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="4ca0c-384">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="4ca0c-385">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="4ca0c-386">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="4ca0c-387">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-387">This stops propagation of the exception.</span></span> <span data-ttu-id="4ca0c-388">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="4ca0c-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="4ca0c-389">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-389">Only an action filter can do that.</span></span>

<span data-ttu-id="4ca0c-390">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-390">Exception filters:</span></span>

* <span data-ttu-id="4ca0c-391">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="4ca0c-392">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="4ca0c-393">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="4ca0c-394">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="4ca0c-395">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="4ca0c-396">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="4ca0c-397">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="4ca0c-397">Result filters</span></span>

<span data-ttu-id="4ca0c-398">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-398">Result filters:</span></span>

* <span data-ttu-id="4ca0c-399">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-399">Implement an interface:</span></span>
  * <span data-ttu-id="4ca0c-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ca0c-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="4ca0c-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ca0c-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="4ca0c-402">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="4ca0c-403">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ca0c-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="4ca0c-404">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ca0c-405">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="4ca0c-406">Une action qui retourne une vue comprend tout le traitement Razor dans le cadre du <xref:Microsoft.AspNetCore.Mvc.ViewResult> en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="4ca0c-407">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="4ca0c-408">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="4ca0c-409">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="4ca0c-410">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="4ca0c-411">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="4ca0c-412">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="4ca0c-413">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="4ca0c-414">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="4ca0c-415">Levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="4ca0c-416">Empêche l’exécution du résultat de l’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="4ca0c-417">Est traité comme un échec au lieu d’un résultat réussi.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ca0c-418">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="4ca0c-419">Si la réponse a déjà été envoyée au client, elle ne peut pas être modifiée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="4ca0c-420">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="4ca0c-421">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="4ca0c-422">La définition `Exception` de la valeur null gère efficacement une exception et empêche la levée de l’exception plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="4ca0c-423">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="4ca0c-424">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="4ca0c-425">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="4ca0c-426">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="4ca0c-427">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="4ca0c-428">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="4ca0c-429">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ca0c-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="4ca0c-430">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="4ca0c-431">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-431">This includes action results produced by:</span></span>

* <span data-ttu-id="4ca0c-432">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="4ca0c-433">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-433">Exception filters.</span></span>

<span data-ttu-id="4ca0c-434">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="4ca0c-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4ca0c-435">IFilterFactory</span></span>

<span data-ttu-id="4ca0c-436">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="4ca0c-437">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="4ca0c-438">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="4ca0c-439">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="4ca0c-440">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="4ca0c-441">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="4ca0c-442">Le filtre est appliqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="4ca0c-443">Testez le code précédent en exécutant l' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="4ca0c-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="4ca0c-444">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="4ca0c-445">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="4ca0c-446">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="4ca0c-447">**Auteur :**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="4ca0c-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="4ca0c-448">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="4ca0c-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="4ca0c-449">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="4ca0c-449">**internal:** `My header`</span></span>

<span data-ttu-id="4ca0c-450">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="4ca0c-451">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="4ca0c-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="4ca0c-452">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="4ca0c-453">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="4ca0c-454">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="4ca0c-455">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ca0c-456">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ca0c-457">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="4ca0c-458">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="4ca0c-459">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="4ca0c-460">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="4ca0c-461">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="4ca0c-462">Toutefois, les filtres diffèrent des intergiciels (middleware) en ce sens qu’ils font partie du runtime, ce qui signifie qu’ils ont accès au contexte et aux constructions.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="4ca0c-463">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="4ca0c-464">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="4ca0c-465">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="4ca0c-466">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="4ca0c-467">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="4ca0c-467">Next actions</span></span>

* <span data-ttu-id="4ca0c-468">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="4ca0c-469">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ca0c-470">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4ca0c-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4ca0c-471">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="4ca0c-472">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="4ca0c-473">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="4ca0c-474">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="4ca0c-475">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="4ca0c-476">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="4ca0c-477">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="4ca0c-478">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="4ca0c-479">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="4ca0c-480">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="4ca0c-481">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-481">How filters work</span></span>

<span data-ttu-id="4ca0c-482">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="4ca0c-483">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La requête est traitée via un autre intergiciel, un intergiciel de routage, une sélection d’action et le pipeline d’appels d’action ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="4ca0c-486">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-486">Filter types</span></span>

<span data-ttu-id="4ca0c-487">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="4ca0c-488">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="4ca0c-489">Les filtres d’autorisations peuvent court-circuiter le pipeline si la requête n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="4ca0c-490">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="4ca0c-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="4ca0c-491">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-491">Run after authorization.</span></span>  
  * <span data-ttu-id="4ca0c-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> peut exécuter du code avant le reste du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="4ca0c-493">Par exemple, `OnResourceExecuting` peut exécuter du code avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="4ca0c-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> peut exécuter du code une fois que le reste du pipeline terminé.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="4ca0c-495">Les [filtres d’actions](#action-filters) peuvent exécuter du code juste avant et juste après l’appel d’une méthode d’action individuelle.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="4ca0c-496">Ils peuvent être utilisés pour manipuler les arguments passés à une action et le résultat retourné depuis l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="4ca0c-497">Les filtres d’action **ne sont pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="4ca0c-498">Les [filtres d’exceptions](#exception-filters) sont utilisés pour appliquer des stratégies globales à des exceptions non gérées qui se produisent avant que des éléments aient été écrits dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="4ca0c-499">Les [filtres de résultats](#result-filters) peuvent exécuter du code juste avant et juste après l’exécution des résultats d’une action individuelle.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="4ca0c-500">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="4ca0c-501">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="4ca0c-502">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="4ca0c-505">Implémentation</span><span class="sxs-lookup"><span data-stu-id="4ca0c-505">Implementation</span></span>

<span data-ttu-id="4ca0c-506">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="4ca0c-507">Les filtres synchrones peuvent exécuter du code avant (`On-Stage-Executing`) et après (`On-Stage-Executed`) leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="4ca0c-508">Par exemple, `OnActionExecuting` est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="4ca0c-509">`OnActionExecuted` est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ca0c-510">Les filtres asynchrones définissent une méthode `On-Stage-ExecutionAsync` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="4ca0c-511">Dans le code précédent, le `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="4ca0c-512">Chacune des méthodes `On-Stage-ExecutionAsync` prennent un `FilterType-ExecutionDelegate` qui exécute l’étape de pipeline du filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="4ca0c-513">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-513">Multiple filter stages</span></span>

<span data-ttu-id="4ca0c-514">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="4ca0c-515">Par exemple, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implémente `IActionFilter`, `IResultFilter` et leurs équivalents asynchrones.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="4ca0c-516">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="4ca0c-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="4ca0c-517">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4ca0c-518">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4ca0c-519">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="4ca0c-520">Quand vous utilisez des classes abstraites comme <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, vous devez remplacer seulement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="4ca0c-521">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="4ca0c-521">Built-in filter attributes</span></span>

<span data-ttu-id="4ca0c-522">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="4ca0c-523">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="4ca0c-524">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="4ca0c-525">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="4ca0c-526">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="4ca0c-527">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="4ca0c-528">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="4ca0c-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="4ca0c-529">Un filtre peut être ajouté au pipeline à l’une des trois *étendues*suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="4ca0c-530">À l’aide d’un attribut sur une action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="4ca0c-531">À l’aide d’un attribut sur un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="4ca0c-532">Dans le monde entier pour tous les contrôleurs et actions, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="4ca0c-533">Le code précédent ajoute trois filtres globalement à l’aide de la collection [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="4ca0c-534">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="4ca0c-534">Default order of execution</span></span>

<span data-ttu-id="4ca0c-535">Lorsqu’il existe plusieurs filtres *du même type*, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="4ca0c-536">Filtres globaux-filtres de classe surround.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-536">Global filters surround class filters.</span></span> <span data-ttu-id="4ca0c-537">Les filtres de classe entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-537">Class filters surround method filters.</span></span>

<span data-ttu-id="4ca0c-538">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="4ca0c-539">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-539">The filter sequence:</span></span>

* <span data-ttu-id="4ca0c-540">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="4ca0c-541">Le code *avant* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="4ca0c-542">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="4ca0c-543">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="4ca0c-544">Le code *après* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="4ca0c-545">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="4ca0c-546">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="4ca0c-547">Séquence</span><span class="sxs-lookup"><span data-stu-id="4ca0c-547">Sequence</span></span> | <span data-ttu-id="4ca0c-548">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="4ca0c-548">Filter scope</span></span> | <span data-ttu-id="4ca0c-549">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="4ca0c-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="4ca0c-550">1</span><span class="sxs-lookup"><span data-stu-id="4ca0c-550">1</span></span> | <span data-ttu-id="4ca0c-551">Globale</span><span class="sxs-lookup"><span data-stu-id="4ca0c-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-552">2</span><span class="sxs-lookup"><span data-stu-id="4ca0c-552">2</span></span> | <span data-ttu-id="4ca0c-553">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ca0c-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-554">3</span><span class="sxs-lookup"><span data-stu-id="4ca0c-554">3</span></span> | <span data-ttu-id="4ca0c-555">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ca0c-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-556">4</span><span class="sxs-lookup"><span data-stu-id="4ca0c-556">4</span></span> | <span data-ttu-id="4ca0c-557">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ca0c-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ca0c-558">5</span><span class="sxs-lookup"><span data-stu-id="4ca0c-558">5</span></span> | <span data-ttu-id="4ca0c-559">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ca0c-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4ca0c-560">6</span><span class="sxs-lookup"><span data-stu-id="4ca0c-560">6</span></span> | <span data-ttu-id="4ca0c-561">Globale</span><span class="sxs-lookup"><span data-stu-id="4ca0c-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="4ca0c-562">Cette séquence montre que :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-562">This sequence shows:</span></span>

* <span data-ttu-id="4ca0c-563">Le filtre de méthode est imbriqué dans le filtre de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="4ca0c-564">Le filtre de contrôleur est imbriqué dans le filtre global.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="4ca0c-565">Filtres au niveau du contrôleur et de la Razor page</span><span class="sxs-lookup"><span data-stu-id="4ca0c-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="4ca0c-566">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="4ca0c-567">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-567">These methods:</span></span>

* <span data-ttu-id="4ca0c-568">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="4ca0c-569">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="4ca0c-570">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="4ca0c-571">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="4ca0c-572">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="4ca0c-573">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="4ca0c-574">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-574">The `TestController`:</span></span>

* <span data-ttu-id="4ca0c-575">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="4ca0c-576">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="4ca0c-577">La navigation vers `https://localhost:5001/Test/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="4ca0c-578">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="4ca0c-579">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="4ca0c-579">Overriding the default order</span></span>

<span data-ttu-id="4ca0c-580">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="4ca0c-581">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="4ca0c-582">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="4ca0c-583">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="4ca0c-584">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="4ca0c-585">La propriété `Order` peut être définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="4ca0c-586">Prenez en compte les mêmes 3 filtres d’actions indiqués dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="4ca0c-587">Si la propriété `Order` du contrôleur et les filtres globaux sont définis sur 1 et 2 respectivement, l’ordre d’exécution est inversé.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="4ca0c-588">Séquence</span><span class="sxs-lookup"><span data-stu-id="4ca0c-588">Sequence</span></span> | <span data-ttu-id="4ca0c-589">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="4ca0c-589">Filter scope</span></span> | <span data-ttu-id="4ca0c-590">Propriété `Order`</span><span class="sxs-lookup"><span data-stu-id="4ca0c-590">`Order` property</span></span> | <span data-ttu-id="4ca0c-591">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="4ca0c-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="4ca0c-592">1</span><span class="sxs-lookup"><span data-stu-id="4ca0c-592">1</span></span> | <span data-ttu-id="4ca0c-593">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ca0c-593">Method</span></span> | <span data-ttu-id="4ca0c-594">0</span><span class="sxs-lookup"><span data-stu-id="4ca0c-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-595">2</span><span class="sxs-lookup"><span data-stu-id="4ca0c-595">2</span></span> | <span data-ttu-id="4ca0c-596">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ca0c-596">Controller</span></span> | <span data-ttu-id="4ca0c-597">1</span><span class="sxs-lookup"><span data-stu-id="4ca0c-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-598">3</span><span class="sxs-lookup"><span data-stu-id="4ca0c-598">3</span></span> | <span data-ttu-id="4ca0c-599">Globale</span><span class="sxs-lookup"><span data-stu-id="4ca0c-599">Global</span></span> | <span data-ttu-id="4ca0c-600">2</span><span class="sxs-lookup"><span data-stu-id="4ca0c-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="4ca0c-601">4</span><span class="sxs-lookup"><span data-stu-id="4ca0c-601">4</span></span> | <span data-ttu-id="4ca0c-602">Globale</span><span class="sxs-lookup"><span data-stu-id="4ca0c-602">Global</span></span> | <span data-ttu-id="4ca0c-603">2</span><span class="sxs-lookup"><span data-stu-id="4ca0c-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="4ca0c-604">5</span><span class="sxs-lookup"><span data-stu-id="4ca0c-604">5</span></span> | <span data-ttu-id="4ca0c-605">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="4ca0c-605">Controller</span></span> | <span data-ttu-id="4ca0c-606">1</span><span class="sxs-lookup"><span data-stu-id="4ca0c-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="4ca0c-607">6</span><span class="sxs-lookup"><span data-stu-id="4ca0c-607">6</span></span> | <span data-ttu-id="4ca0c-608">Méthode</span><span class="sxs-lookup"><span data-stu-id="4ca0c-608">Method</span></span> | <span data-ttu-id="4ca0c-609">0</span><span class="sxs-lookup"><span data-stu-id="4ca0c-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="4ca0c-610">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="4ca0c-611">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="4ca0c-612">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="4ca0c-613">Pour les filtres intégrés, l’étendue détermine l’ordre, sauf si `Order` est défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="4ca0c-614">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="4ca0c-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="4ca0c-615">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="4ca0c-616">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4ca0c-617">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="4ca0c-618">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="4ca0c-619">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="4ca0c-620">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="4ca0c-621">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="4ca0c-622">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="4ca0c-623">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="4ca0c-624">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="4ca0c-624">Dependency injection</span></span>

<span data-ttu-id="4ca0c-625">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="4ca0c-626">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="4ca0c-627">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="4ca0c-628">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-628">A type-activated filter means:</span></span>

* <span data-ttu-id="4ca0c-629">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-629">An instance is created for each request.</span></span>
* <span data-ttu-id="4ca0c-630">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="4ca0c-631">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="4ca0c-632">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="4ca0c-633">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="4ca0c-634">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="4ca0c-635">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="4ca0c-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="4ca0c-637">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="4ca0c-638">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-638">Loggers are available from DI.</span></span> <span data-ttu-id="4ca0c-639">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="4ca0c-640">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="4ca0c-641">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-641">Logging added to filters:</span></span>

* <span data-ttu-id="4ca0c-642">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="4ca0c-643">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="4ca0c-644">Les filtres intégrés enregistrent des actions et des événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="4ca0c-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ca0c-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="4ca0c-646">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="4ca0c-647">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="4ca0c-648">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ca0c-649">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="4ca0c-650">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="4ca0c-651">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="4ca0c-652">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ca0c-653">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="4ca0c-654">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="4ca0c-655">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="4ca0c-656">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="4ca0c-657">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ca0c-658">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ca0c-659">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="4ca0c-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4ca0c-660">TypeFilterAttribute</span></span>

<span data-ttu-id="4ca0c-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="4ca0c-662">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="4ca0c-663">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="4ca0c-664">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="4ca0c-665">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="4ca0c-666">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="4ca0c-667">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="4ca0c-668">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4ca0c-669">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="4ca0c-670">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="4ca0c-671">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="4ca0c-672">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="4ca0c-672">Authorization filters</span></span>

<span data-ttu-id="4ca0c-673">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-673">Authorization filters:</span></span>

* <span data-ttu-id="4ca0c-674">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="4ca0c-675">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-675">Control access to action methods.</span></span>
* <span data-ttu-id="4ca0c-676">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="4ca0c-677">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="4ca0c-678">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="4ca0c-679">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="4ca0c-680">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-680">Calls the authorization system.</span></span>
* <span data-ttu-id="4ca0c-681">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-681">Does not authorize requests.</span></span>

<span data-ttu-id="4ca0c-682">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="4ca0c-683">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-683">The exception will not be handled.</span></span>
* <span data-ttu-id="4ca0c-684">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="4ca0c-685">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="4ca0c-686">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="4ca0c-687">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="4ca0c-687">Resource filters</span></span>

<span data-ttu-id="4ca0c-688">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-688">Resource filters:</span></span>

* <span data-ttu-id="4ca0c-689">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="4ca0c-690">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="4ca0c-691">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="4ca0c-692">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="4ca0c-693">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="4ca0c-694">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-694">Resource filter examples:</span></span>

* <span data-ttu-id="4ca0c-695">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="4ca0c-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="4ca0c-697">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="4ca0c-698">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="4ca0c-699">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="4ca0c-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4ca0c-700">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="4ca0c-701">Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="4ca0c-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="4ca0c-702">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="4ca0c-703">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-703">Action filters:</span></span>

* <span data-ttu-id="4ca0c-704">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="4ca0c-705">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="4ca0c-706">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4ca0c-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="4ca0c-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> : permet de lire les entrées vers une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="4ca0c-709"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="4ca0c-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="4ca0c-711">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="4ca0c-712">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="4ca0c-713">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ca0c-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="4ca0c-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ca0c-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="4ca0c-717">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-717">Setting this property to null:</span></span>

  * <span data-ttu-id="4ca0c-718">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="4ca0c-719">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="4ca0c-720">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="4ca0c-721">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="4ca0c-722">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="4ca0c-723">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="4ca0c-724">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="4ca0c-725">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="4ca0c-726">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-726">Validate model state.</span></span>
* <span data-ttu-id="4ca0c-727">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="4ca0c-728">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="4ca0c-729">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="4ca0c-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4ca0c-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="4ca0c-732">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="4ca0c-733">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="4ca0c-734">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="4ca0c-735">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="4ca0c-735">Exception filters</span></span>

<span data-ttu-id="4ca0c-736">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-736">Exception filters:</span></span>

* <span data-ttu-id="4ca0c-737">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="4ca0c-738">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="4ca0c-739">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="4ca0c-740">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-740">Exception filters:</span></span>

* <span data-ttu-id="4ca0c-741">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-741">Don't have before and after events.</span></span>
* <span data-ttu-id="4ca0c-742">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="4ca0c-743">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="4ca0c-744">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="4ca0c-745">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="4ca0c-746">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-746">This stops propagation of the exception.</span></span> <span data-ttu-id="4ca0c-747">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="4ca0c-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="4ca0c-748">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-748">Only an action filter can do that.</span></span>

<span data-ttu-id="4ca0c-749">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-749">Exception filters:</span></span>

* <span data-ttu-id="4ca0c-750">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="4ca0c-751">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="4ca0c-752">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="4ca0c-753">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="4ca0c-754">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="4ca0c-755">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="4ca0c-756">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="4ca0c-756">Result filters</span></span>

<span data-ttu-id="4ca0c-757">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-757">Result filters:</span></span>

* <span data-ttu-id="4ca0c-758">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-758">Implement an interface:</span></span>
  * <span data-ttu-id="4ca0c-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ca0c-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="4ca0c-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="4ca0c-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="4ca0c-761">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="4ca0c-762">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ca0c-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="4ca0c-763">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4ca0c-764">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="4ca0c-765">Une action retournant un affichage inclut tous les traitements Razor dans le cadre de la <xref:Microsoft.AspNetCore.Mvc.ViewResult> à exécuter.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="4ca0c-766">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="4ca0c-767">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="4ca0c-768">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="4ca0c-769">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="4ca0c-770">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="4ca0c-771">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="4ca0c-772">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="4ca0c-773">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="4ca0c-774">La levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="4ca0c-775">Empêche l’exécution du résultat d’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="4ca0c-776">Est traitée comme une erreur et non comme une réussite.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4ca0c-777">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="4ca0c-778">Si la réponse a déjà été envoyée au client, elle ne peut plus être modifiée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="4ca0c-779">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="4ca0c-780">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="4ca0c-781">Le paramètre de `Exception` sur Null gère effectivement une exception et évite à l’exception d’être à nouveau levée par ASP.NET Core plus loin dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="4ca0c-782">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="4ca0c-783">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="4ca0c-784">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="4ca0c-785">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="4ca0c-786">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="4ca0c-787">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="4ca0c-788">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="4ca0c-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="4ca0c-789">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="4ca0c-790">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-790">This includes action results produced by:</span></span>

* <span data-ttu-id="4ca0c-791">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="4ca0c-792">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-792">Exception filters.</span></span>

<span data-ttu-id="4ca0c-793">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="4ca0c-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4ca0c-794">IFilterFactory</span></span>

<span data-ttu-id="4ca0c-795">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="4ca0c-796">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="4ca0c-797">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="4ca0c-798">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="4ca0c-799">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="4ca0c-800">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="4ca0c-801">Le code précédent peut être testé en exécutant l’[échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="4ca0c-802">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="4ca0c-803">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="4ca0c-804">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="4ca0c-805">**Auteur :**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="4ca0c-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="4ca0c-806">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="4ca0c-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="4ca0c-807">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="4ca0c-807">**internal:** `My header`</span></span>

<span data-ttu-id="4ca0c-808">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="4ca0c-809">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="4ca0c-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="4ca0c-810">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="4ca0c-811">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="4ca0c-812">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="4ca0c-813">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4ca0c-814">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4ca0c-815">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="4ca0c-816">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="4ca0c-817">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="4ca0c-818">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="4ca0c-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="4ca0c-819">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="4ca0c-820">Les filtres diffèrent cependant d’un intergiciel dans la mesure où ils font partie du runtime ASP.NET Core, ce qui signifie qu’ils ont accès au contexte et aux constructions ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="4ca0c-821">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="4ca0c-822">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="4ca0c-823">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="4ca0c-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="4ca0c-824">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="4ca0c-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="4ca0c-825">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="4ca0c-825">Next actions</span></span>

* <span data-ttu-id="4ca0c-826">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="4ca0c-827">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="4ca0c-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
