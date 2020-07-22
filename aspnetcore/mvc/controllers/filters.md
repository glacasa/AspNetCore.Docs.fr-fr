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
ms.openlocfilehash: 96d24940af6c591e3c02bfa26ed9d7d6ea60d27d
ms.sourcegitcommit: d00a200bc8347af794b24184da14ad5c8b6bba9a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86869976"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="2125a-103">Filtres dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2125a-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2125a-104">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2125a-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2125a-105">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="2125a-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="2125a-106">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="2125a-107">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="2125a-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="2125a-108">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="2125a-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="2125a-109">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="2125a-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="2125a-110">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="2125a-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="2125a-111">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="2125a-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="2125a-112">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="2125a-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="2125a-113">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="2125a-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="2125a-114">Les filtres ne fonctionnent pas directement avec les [ Razor composants](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="2125a-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="2125a-115">Un filtre peut uniquement affecter indirectement un composant lorsque :</span><span class="sxs-lookup"><span data-stu-id="2125a-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="2125a-116">Le composant est incorporé dans une page ou une vue.</span><span class="sxs-lookup"><span data-stu-id="2125a-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="2125a-117">La page ou le contrôleur/la vue utilise le filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="2125a-118">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2125a-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="2125a-119">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-119">How filters work</span></span>

<span data-ttu-id="2125a-120">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="2125a-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="2125a-121">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="2125a-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La demande est traitée par un autre intergiciel, un intergiciel (middleware) de routage, une sélection d’action et le pipeline d’appel d’action.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="2125a-124">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-124">Filter types</span></span>

<span data-ttu-id="2125a-125">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="2125a-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="2125a-126">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="2125a-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="2125a-127">Filtres d’autorisation court-circuiter le pipeline si la demande n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="2125a-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="2125a-128">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="2125a-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="2125a-129">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="2125a-129">Run after authorization.</span></span>  
  * <span data-ttu-id="2125a-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>exécute du code avant le reste du pipeline de filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="2125a-131">Par exemple, `OnResourceExecuting` exécute le code avant la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="2125a-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="2125a-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>exécute du code une fois que le reste du pipeline est terminé.</span><span class="sxs-lookup"><span data-stu-id="2125a-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="2125a-133">[Filtres d’action](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="2125a-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="2125a-134">Exécutez le code juste avant et après l’appel d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="2125a-135">Peut modifier les arguments passés dans une action.</span><span class="sxs-lookup"><span data-stu-id="2125a-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="2125a-136">Peut modifier le résultat retourné par l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="2125a-137">Ne sont **pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="2125a-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="2125a-138">Les [filtres d’exception](#exception-filters) appliquent des stratégies globales aux exceptions non gérées qui se produisent avant l’écriture du corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="2125a-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="2125a-139">Les [filtres de résultats](#result-filters) exécutent le code immédiatement avant et après l’exécution des résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="2125a-140">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="2125a-141">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="2125a-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="2125a-142">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="2125a-145">Implémentation</span><span class="sxs-lookup"><span data-stu-id="2125a-145">Implementation</span></span>

<span data-ttu-id="2125a-146">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="2125a-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="2125a-147">Les filtres synchrones exécutent le code avant et après leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="2125a-148">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="2125a-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2125a-150">Dans le code précédent, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) est une fonction utilitaire dans l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="2125a-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="2125a-151">Les filtres asynchrones définissent une `On-Stage-ExecutionAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="2125a-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="2125a-152">Par exemple, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="2125a-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="2125a-153">Dans le code précédent, `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="2125a-154">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-154">Multiple filter stages</span></span>

<span data-ttu-id="2125a-155">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="2125a-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="2125a-156">Par exemple, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implémente :</span><span class="sxs-lookup"><span data-stu-id="2125a-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="2125a-157">Synchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> et<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="2125a-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="2125a-158">Asynchrone : <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> et<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2125a-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="2125a-159">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="2125a-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="2125a-160">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="2125a-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="2125a-161">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="2125a-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="2125a-162">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="2125a-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="2125a-163">Lorsque vous utilisez des classes abstraites telles que <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , remplacez uniquement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="2125a-164">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="2125a-164">Built-in filter attributes</span></span>

<span data-ttu-id="2125a-165">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="2125a-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="2125a-166">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="2125a-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="2125a-167">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="2125a-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="2125a-168">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="2125a-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="2125a-169">Utilisez un outil tel que les [outils de développement du navigateur](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) pour examiner les en-têtes.</span><span class="sxs-lookup"><span data-stu-id="2125a-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="2125a-170">Sous **en-têtes de réponse**, `author: Rick Anderson` est affiché.</span><span class="sxs-lookup"><span data-stu-id="2125a-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="2125a-171">Le code suivant implémente un `ActionFilterAttribute` qui :</span><span class="sxs-lookup"><span data-stu-id="2125a-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="2125a-172">Lit le titre et le nom à partir du système de configuration.</span><span class="sxs-lookup"><span data-stu-id="2125a-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="2125a-173">Contrairement à l’exemple précédent, le code suivant ne nécessite pas l’ajout de paramètres de filtre au code.</span><span class="sxs-lookup"><span data-stu-id="2125a-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="2125a-174">Ajoute le titre et le nom à l’en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="2125a-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2125a-175">Les options de configuration sont fournies par le [système de configuration](xref:fundamentals/configuration/index) à l’aide du modèle d' [options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="2125a-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="2125a-176">Par exemple, à partir de l' *appsettings.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="2125a-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="2125a-177">Dans le `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2125a-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="2125a-178">La `PositionOptions` classe est ajoutée au conteneur de services avec la `"Position"` zone de configuration.</span><span class="sxs-lookup"><span data-stu-id="2125a-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="2125a-179">Le `MyActionFilterAttribute` est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="2125a-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="2125a-180">Le code suivant illustre la `PositionOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="2125a-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="2125a-181">Le code suivant applique `MyActionFilterAttribute` à la `Index2` méthode :</span><span class="sxs-lookup"><span data-stu-id="2125a-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="2125a-182">Sous **les en-têtes de réponse**, `author: Rick Anderson` et `Editor: Joe Smith` s’affiche lorsque le `Sample/Index2` point de terminaison est appelé.</span><span class="sxs-lookup"><span data-stu-id="2125a-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="2125a-183">Le code suivant applique le `MyActionFilterAttribute` et le `AddHeaderAttribute` à la Razor page :</span><span class="sxs-lookup"><span data-stu-id="2125a-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2125a-184">Les filtres ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="2125a-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="2125a-185">Ils peuvent être appliqués au modèle de Razor page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="2125a-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="2125a-186">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="2125a-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="2125a-187">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="2125a-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="2125a-188">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="2125a-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="2125a-189">Un filtre peut être ajouté au pipeline à l’une des trois *étendues*suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="2125a-190">Utilisation d’un attribut sur une action de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="2125a-191">Les attributs de filtre ne peuvent pas être appliqués aux Razor méthodes de gestionnaire de pages.</span><span class="sxs-lookup"><span data-stu-id="2125a-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="2125a-192">Utilisation d’un attribut sur un contrôleur ou une Razor page.</span><span class="sxs-lookup"><span data-stu-id="2125a-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="2125a-193">Globalement pour tous les contrôleurs, actions et Razor pages, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2125a-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="2125a-194">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="2125a-194">Default order of execution</span></span>

<span data-ttu-id="2125a-195">Quand il existe plusieurs filtres pour une étape particulière du pipeline, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="2125a-196">Les filtres globaux entourent les filtres de classe, qui à leur tour entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="2125a-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="2125a-197">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="2125a-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="2125a-198">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="2125a-198">The filter sequence:</span></span>

* <span data-ttu-id="2125a-199">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="2125a-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="2125a-200">*Avant* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="2125a-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="2125a-201">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="2125a-202">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="2125a-203">*Après* le code du contrôleur et des Razor filtres de page.</span><span class="sxs-lookup"><span data-stu-id="2125a-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="2125a-204">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="2125a-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="2125a-205">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="2125a-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="2125a-206">Séquence</span><span class="sxs-lookup"><span data-stu-id="2125a-206">Sequence</span></span> | <span data-ttu-id="2125a-207">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="2125a-207">Filter scope</span></span> | <span data-ttu-id="2125a-208">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="2125a-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="2125a-209">1</span><span class="sxs-lookup"><span data-stu-id="2125a-209">1</span></span> | <span data-ttu-id="2125a-210">Global</span><span class="sxs-lookup"><span data-stu-id="2125a-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2125a-211">2</span><span class="sxs-lookup"><span data-stu-id="2125a-211">2</span></span> | <span data-ttu-id="2125a-212">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="2125a-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="2125a-213">3</span><span class="sxs-lookup"><span data-stu-id="2125a-213">3</span></span> | <span data-ttu-id="2125a-214">Méthode</span><span class="sxs-lookup"><span data-stu-id="2125a-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2125a-215">4</span><span class="sxs-lookup"><span data-stu-id="2125a-215">4</span></span> | <span data-ttu-id="2125a-216">Méthode</span><span class="sxs-lookup"><span data-stu-id="2125a-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2125a-217">5</span><span class="sxs-lookup"><span data-stu-id="2125a-217">5</span></span> | <span data-ttu-id="2125a-218">Contrôleur ou Razor page</span><span class="sxs-lookup"><span data-stu-id="2125a-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2125a-219">6</span><span class="sxs-lookup"><span data-stu-id="2125a-219">6</span></span> | <span data-ttu-id="2125a-220">Global</span><span class="sxs-lookup"><span data-stu-id="2125a-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="2125a-221">Filtres au niveau du contrôleur</span><span class="sxs-lookup"><span data-stu-id="2125a-221">Controller level filters</span></span>

<span data-ttu-id="2125a-222">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="2125a-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="2125a-223">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="2125a-223">These methods:</span></span>

* <span data-ttu-id="2125a-224">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="2125a-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="2125a-225">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="2125a-226">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="2125a-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="2125a-227">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="2125a-228">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="2125a-229">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2125a-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="2125a-230">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="2125a-230">The `TestController`:</span></span>

* <span data-ttu-id="2125a-231">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="2125a-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="2125a-232">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="2125a-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="2125a-233">La navigation vers `https://localhost:5001/Test2/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2125a-233">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="2125a-234">Les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="2125a-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="2125a-235">Les filtres au niveau du contrôleur **ne peuvent pas** être configurés pour s’exécuter après les filtres appliqués aux méthodes.</span><span class="sxs-lookup"><span data-stu-id="2125a-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="2125a-236">L’ordre est expliqué dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="2125a-236">Order is explained in the next section.</span></span>

<span data-ttu-id="2125a-237">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="2125a-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="2125a-238">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="2125a-238">Overriding the default order</span></span>

<span data-ttu-id="2125a-239">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="2125a-240">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="2125a-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="2125a-241">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="2125a-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="2125a-242">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="2125a-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="2125a-243">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="2125a-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="2125a-244">La `Order` propriété est définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="2125a-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="2125a-245">Examinez les deux filtres d’action dans le contrôleur suivant :</span><span class="sxs-lookup"><span data-stu-id="2125a-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="2125a-246">Un filtre global est ajouté dans `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2125a-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="2125a-247">Les 3 filtres s’exécutent dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="2125a-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="2125a-248">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="2125a-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="2125a-249">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="2125a-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="2125a-250">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="2125a-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="2125a-251">Comme mentionné précédemment, les filtres au niveau du contrôleur définissent la propriété [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) sur `int.MinValue` pour les filtres intégrés, Scope détermine l’ordre, à moins que `Order` ne soit défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="2125a-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="2125a-252">Dans le code précédent, `MySampleActionFilter` a une portée globale pour qu’elle s’exécute avant `MyAction2FilterAttribute` , qui a une portée de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="2125a-253">Pour effectuer `MyAction2FilterAttribute` l’exécution en premier, définissez l’ordre sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="2125a-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="2125a-254">Pour que le filtre global `MySampleActionFilter` s’exécute en premier, définissez `Order` sur `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="2125a-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="2125a-255">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="2125a-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="2125a-256">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="2125a-257">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="2125a-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2125a-258">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="2125a-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="2125a-259">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="2125a-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="2125a-260">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="2125a-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="2125a-261">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="2125a-262">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="2125a-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="2125a-263">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="2125a-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="2125a-264">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="2125a-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="2125a-265">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="2125a-265">Dependency injection</span></span>

<span data-ttu-id="2125a-266">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="2125a-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="2125a-267">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="2125a-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="2125a-268">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="2125a-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="2125a-269">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="2125a-269">A type-activated filter means:</span></span>

* <span data-ttu-id="2125a-270">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="2125a-270">An instance is created for each request.</span></span>
* <span data-ttu-id="2125a-271">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="2125a-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="2125a-272">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2125a-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="2125a-273">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="2125a-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="2125a-274">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="2125a-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="2125a-275">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="2125a-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="2125a-276">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="2125a-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="2125a-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="2125a-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="2125a-278">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="2125a-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="2125a-279">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-279">Loggers are available from DI.</span></span> <span data-ttu-id="2125a-280">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="2125a-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="2125a-281">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="2125a-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="2125a-282">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="2125a-282">Logging added to filters:</span></span>

* <span data-ttu-id="2125a-283">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="2125a-284">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="2125a-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="2125a-285">Les filtres intégrés consignent les actions et les événements du Framework.</span><span class="sxs-lookup"><span data-stu-id="2125a-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="2125a-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2125a-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="2125a-287">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2125a-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="2125a-288">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="2125a-289">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="2125a-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2125a-290">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="2125a-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="2125a-291">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="2125a-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="2125a-292">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="2125a-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="2125a-293">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2125a-294">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="2125a-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="2125a-295">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="2125a-296">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="2125a-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="2125a-297">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="2125a-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="2125a-298">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2125a-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2125a-299">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2125a-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2125a-300">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="2125a-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2125a-301">TypeFilterAttribute</span></span>

<span data-ttu-id="2125a-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="2125a-303">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="2125a-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="2125a-304">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="2125a-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="2125a-305">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="2125a-306">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="2125a-307">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="2125a-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="2125a-308">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="2125a-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="2125a-309">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2125a-310">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="2125a-311">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="2125a-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="2125a-312">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="2125a-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="2125a-313">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="2125a-313">Authorization filters</span></span>

<span data-ttu-id="2125a-314">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="2125a-314">Authorization filters:</span></span>

* <span data-ttu-id="2125a-315">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="2125a-316">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-316">Control access to action methods.</span></span>
* <span data-ttu-id="2125a-317">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="2125a-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="2125a-318">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="2125a-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="2125a-319">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="2125a-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="2125a-320">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="2125a-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="2125a-321">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="2125a-321">Calls the authorization system.</span></span>
* <span data-ttu-id="2125a-322">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="2125a-322">Does not authorize requests.</span></span>

<span data-ttu-id="2125a-323">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="2125a-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="2125a-324">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="2125a-324">The exception will not be handled.</span></span>
* <span data-ttu-id="2125a-325">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="2125a-326">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="2125a-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="2125a-327">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="2125a-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="2125a-328">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="2125a-328">Resource filters</span></span>

<span data-ttu-id="2125a-329">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="2125a-329">Resource filters:</span></span>

* <span data-ttu-id="2125a-330">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="2125a-331">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="2125a-332">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="2125a-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="2125a-333">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="2125a-334">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="2125a-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="2125a-335">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="2125a-335">Resource filter examples:</span></span>

* <span data-ttu-id="2125a-336">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="2125a-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="2125a-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="2125a-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="2125a-338">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="2125a-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="2125a-339">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="2125a-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="2125a-340">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="2125a-340">Action filters</span></span>

<span data-ttu-id="2125a-341">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="2125a-341">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="2125a-342">Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="2125a-342"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="2125a-343">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2125a-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="2125a-344">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="2125a-344">Action filters:</span></span>

* <span data-ttu-id="2125a-345">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="2125a-346">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="2125a-347">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="2125a-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2125a-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="2125a-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-active la lecture des entrées dans une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="2125a-350"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="2125a-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="2125a-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="2125a-352">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="2125a-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="2125a-353">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="2125a-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="2125a-354">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="2125a-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2125a-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="2125a-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2125a-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="2125a-358">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="2125a-358">Setting this property to null:</span></span>

  * <span data-ttu-id="2125a-359">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="2125a-360">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="2125a-361">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="2125a-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="2125a-362">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="2125a-363">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="2125a-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="2125a-364">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="2125a-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="2125a-365">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="2125a-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="2125a-366">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="2125a-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="2125a-367">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="2125a-367">Validate model state.</span></span>
* <span data-ttu-id="2125a-368">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="2125a-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="2125a-369">Les contrôleurs annotés avec l' `[ApiController]` attribut valident automatiquement l’état du modèle et retournent une réponse 400.</span><span class="sxs-lookup"><span data-stu-id="2125a-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="2125a-370">Pour plus d’informations, consultez [Réponses HTTP 400 automatiques](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="2125a-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="2125a-371">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="2125a-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="2125a-372">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="2125a-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="2125a-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2125a-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="2125a-375">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="2125a-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="2125a-376">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="2125a-377">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="2125a-378">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="2125a-378">Exception filters</span></span>

<span data-ttu-id="2125a-379">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="2125a-379">Exception filters:</span></span>

* <span data-ttu-id="2125a-380">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="2125a-381">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="2125a-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="2125a-382">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="2125a-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="2125a-383">Le code suivant teste le filtre d’exception :</span><span class="sxs-lookup"><span data-stu-id="2125a-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="2125a-384">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="2125a-384">Exception filters:</span></span>

* <span data-ttu-id="2125a-385">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="2125a-385">Don't have before and after events.</span></span>
* <span data-ttu-id="2125a-386">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2125a-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="2125a-387">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="2125a-388">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="2125a-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="2125a-389">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="2125a-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="2125a-390">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-390">This stops propagation of the exception.</span></span> <span data-ttu-id="2125a-391">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="2125a-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="2125a-392">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="2125a-392">Only an action filter can do that.</span></span>

<span data-ttu-id="2125a-393">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="2125a-393">Exception filters:</span></span>

* <span data-ttu-id="2125a-394">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="2125a-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="2125a-395">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="2125a-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="2125a-396">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="2125a-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="2125a-397">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="2125a-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="2125a-398">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="2125a-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="2125a-399">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="2125a-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="2125a-400">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="2125a-400">Result filters</span></span>

<span data-ttu-id="2125a-401">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="2125a-401">Result filters:</span></span>

* <span data-ttu-id="2125a-402">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="2125a-402">Implement an interface:</span></span>
  * <span data-ttu-id="2125a-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2125a-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="2125a-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="2125a-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="2125a-405">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="2125a-406">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="2125a-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="2125a-407">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="2125a-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2125a-408">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="2125a-409">Une action qui retourne une vue comprend tout le traitement Razor dans le cadre du <xref:Microsoft.AspNetCore.Mvc.ViewResult> en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="2125a-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="2125a-410">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="2125a-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="2125a-411">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="2125a-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="2125a-412">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="2125a-413">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="2125a-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="2125a-414">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="2125a-415">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="2125a-416">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="2125a-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="2125a-417">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="2125a-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="2125a-418">Levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="2125a-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="2125a-419">Empêche l’exécution du résultat de l’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="2125a-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="2125a-420">Est traité comme un échec au lieu d’un résultat réussi.</span><span class="sxs-lookup"><span data-stu-id="2125a-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2125a-421">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="2125a-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="2125a-422">Si la réponse a déjà été envoyée au client, elle ne peut pas être modifiée.</span><span class="sxs-lookup"><span data-stu-id="2125a-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="2125a-423">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="2125a-424">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="2125a-425">La définition `Exception` de la valeur null gère efficacement une exception et empêche la levée de l’exception plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="2125a-426">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="2125a-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="2125a-427">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="2125a-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="2125a-428">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="2125a-429">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="2125a-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="2125a-430">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="2125a-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="2125a-431">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="2125a-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="2125a-432">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="2125a-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="2125a-433">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="2125a-434">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="2125a-434">This includes action results produced by:</span></span>

* <span data-ttu-id="2125a-435">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="2125a-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="2125a-436">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-436">Exception filters.</span></span>

<span data-ttu-id="2125a-437">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="2125a-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="2125a-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2125a-438">IFilterFactory</span></span>

<span data-ttu-id="2125a-439">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2125a-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="2125a-440">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="2125a-441">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="2125a-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="2125a-442">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="2125a-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="2125a-443">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="2125a-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="2125a-444">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="2125a-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="2125a-445">Le filtre est appliqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2125a-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="2125a-446">Testez le code précédent en exécutant l' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="2125a-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="2125a-447">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="2125a-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="2125a-448">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="2125a-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="2125a-449">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="2125a-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="2125a-450">**Auteur :**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="2125a-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="2125a-451">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="2125a-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="2125a-452">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="2125a-452">**internal:** `My header`</span></span>

<span data-ttu-id="2125a-453">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="2125a-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="2125a-454">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="2125a-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="2125a-455">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="2125a-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="2125a-456">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="2125a-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="2125a-457">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="2125a-458">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2125a-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2125a-459">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2125a-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2125a-460">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="2125a-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="2125a-461">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="2125a-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2125a-462">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="2125a-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="2125a-463">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="2125a-464">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="2125a-465">Toutefois, les filtres diffèrent des intergiciels (middleware) en ce sens qu’ils font partie du runtime, ce qui signifie qu’ils ont accès au contexte et aux constructions.</span><span class="sxs-lookup"><span data-stu-id="2125a-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="2125a-466">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="2125a-467">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="2125a-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="2125a-468">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="2125a-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="2125a-469">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="2125a-470">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="2125a-470">Next actions</span></span>

* <span data-ttu-id="2125a-471">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2125a-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="2125a-472">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="2125a-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2125a-473">Par [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2125a-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2125a-474">Les *filtres* dans ASP.NET Core permettent d’exécuter du code avant ou après des étapes spécifiques dans le pipeline de traitement des requêtes.</span><span class="sxs-lookup"><span data-stu-id="2125a-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="2125a-475">Les filtres intégrés gèrent notamment les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="2125a-476">Autorisation (empêcher un utilisateur non autorisé d’accéder à des ressources).</span><span class="sxs-lookup"><span data-stu-id="2125a-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="2125a-477">Mise en cache des réponses (court-circuitage du pipeline de requêtes pour retourner une réponse mise en cache).</span><span class="sxs-lookup"><span data-stu-id="2125a-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="2125a-478">Il est possible de créer des filtres personnalisés pour gérer les problèmes transversaux.</span><span class="sxs-lookup"><span data-stu-id="2125a-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="2125a-479">Les exemples de problèmes transversaux incluent la gestion des erreurs, la mise en cache, la configuration, l’autorisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="2125a-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="2125a-480">Les filtres évitent la duplication de code.</span><span class="sxs-lookup"><span data-stu-id="2125a-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="2125a-481">Par exemple, un filtre d’exceptions de gestion des erreurs peut servir à consolider la gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="2125a-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="2125a-482">Ce document s’applique aux Razor pages, aux contrôleurs d’API et aux contrôleurs avec des vues.</span><span class="sxs-lookup"><span data-stu-id="2125a-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="2125a-483">[Afficher ou télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2125a-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="2125a-484">Fonctionnement des filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-484">How filters work</span></span>

<span data-ttu-id="2125a-485">Les filtres s’exécutent dans le *pipeline des appels d’action ASP.NET Core*, parfois appelé *pipeline de filtres*.</span><span class="sxs-lookup"><span data-stu-id="2125a-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="2125a-486">Le pipeline de filtres s’exécute après la sélection par ASP.NET Core de l’action à exécuter.</span><span class="sxs-lookup"><span data-stu-id="2125a-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La requête est traitée via un autre intergiciel, un intergiciel de routage, une sélection d’action et le pipeline d’appels d’action ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="2125a-489">Types de filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-489">Filter types</span></span>

<span data-ttu-id="2125a-490">Chaque type de filtre est exécuté dans une étape différente du pipeline de filtres :</span><span class="sxs-lookup"><span data-stu-id="2125a-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="2125a-491">Les [filtres d’autorisations](#authorization-filters) s’exécutent en premier et sont utilisés pour déterminer si l’utilisateur est autorisé pour la requête.</span><span class="sxs-lookup"><span data-stu-id="2125a-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="2125a-492">Les filtres d’autorisations peuvent court-circuiter le pipeline si la requête n’est pas autorisée.</span><span class="sxs-lookup"><span data-stu-id="2125a-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="2125a-493">[Filtres de ressources](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="2125a-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="2125a-494">Exécuter après l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="2125a-494">Run after authorization.</span></span>  
  * <span data-ttu-id="2125a-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> peut exécuter du code avant le reste du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="2125a-496">Par exemple, `OnResourceExecuting` peut exécuter du code avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="2125a-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="2125a-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> peut exécuter du code une fois que le reste du pipeline terminé.</span><span class="sxs-lookup"><span data-stu-id="2125a-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="2125a-498">Les [filtres d’actions](#action-filters) peuvent exécuter du code juste avant et juste après l’appel d’une méthode d’action individuelle.</span><span class="sxs-lookup"><span data-stu-id="2125a-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="2125a-499">Ils peuvent être utilisés pour manipuler les arguments passés à une action et le résultat retourné depuis l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="2125a-500">Les filtres d’action **ne sont pas** pris en charge dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="2125a-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="2125a-501">Les [filtres d’exceptions](#exception-filters) sont utilisés pour appliquer des stratégies globales à des exceptions non gérées qui se produisent avant que des éléments aient été écrits dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="2125a-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="2125a-502">Les [filtres de résultats](#result-filters) peuvent exécuter du code juste avant et juste après l’exécution des résultats d’une action individuelle.</span><span class="sxs-lookup"><span data-stu-id="2125a-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="2125a-503">Ils s’exécutent uniquement au terme de l’exécution de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="2125a-504">Ils sont utiles pour la logique qui doit entourer l’exécution de la vue ou du formateur.</span><span class="sxs-lookup"><span data-stu-id="2125a-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="2125a-505">Le diagramme suivant montre comment ces types de filtres interagissent dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La requête est traitée à travers les filtres d’autorisations, les filtres de ressources, la liaison de modèle, les filtres d’actions, l’exécution d’actions et la conversion des résultats d’actions, les filtres d’exceptions, les filtres de résultats et l’exécution de résultats.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="2125a-508">Implémentation</span><span class="sxs-lookup"><span data-stu-id="2125a-508">Implementation</span></span>

<span data-ttu-id="2125a-509">Les filtres prennent en charge les implémentations synchrones et asynchrones via différentes définitions d’interface.</span><span class="sxs-lookup"><span data-stu-id="2125a-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="2125a-510">Les filtres synchrones peuvent exécuter du code avant (`On-Stage-Executing`) et après (`On-Stage-Executed`) leur étape de pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="2125a-511">Par exemple, `OnActionExecuting` est appelé avant l’appel de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="2125a-512">`OnActionExecuted` est appelé après le retour de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2125a-513">Les filtres asynchrones définissent une méthode `On-Stage-ExecutionAsync` :</span><span class="sxs-lookup"><span data-stu-id="2125a-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="2125a-514">Dans le code précédent, le `SampleAsyncActionFilter` a un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) qui exécute la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="2125a-515">Chacune des méthodes `On-Stage-ExecutionAsync` prennent un `FilterType-ExecutionDelegate` qui exécute l’étape de pipeline du filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="2125a-516">Plusieurs étapes de filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-516">Multiple filter stages</span></span>

<span data-ttu-id="2125a-517">Vous pouvez implémenter des interfaces pour plusieurs étapes de filtres dans une même classe.</span><span class="sxs-lookup"><span data-stu-id="2125a-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="2125a-518">Par exemple, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implémente `IActionFilter`, `IResultFilter` et leurs équivalents asynchrones.</span><span class="sxs-lookup"><span data-stu-id="2125a-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="2125a-519">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="2125a-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="2125a-520">Le runtime vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="2125a-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="2125a-521">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="2125a-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="2125a-522">Si des interfaces synchrones et asynchrones sont implémentées dans une classe, seule la méthode asynchrone est appelée.</span><span class="sxs-lookup"><span data-stu-id="2125a-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="2125a-523">Quand vous utilisez des classes abstraites comme <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, vous devez remplacer seulement les méthodes synchrones ou la méthode asynchrone pour chaque type de filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="2125a-524">Attributs de filtre intégrés</span><span class="sxs-lookup"><span data-stu-id="2125a-524">Built-in filter attributes</span></span>

<span data-ttu-id="2125a-525">ASP.NET Core inclut les filtres intégrés basés sur des attributs que vous pouvez définir dans une sous-classe et personnaliser.</span><span class="sxs-lookup"><span data-stu-id="2125a-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="2125a-526">Par exemple, le filtre de résultats suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="2125a-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="2125a-527">Les attributs autorisent les filtres à accepter des arguments, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="2125a-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="2125a-528">Appliquez le `AddHeaderAttribute` à un contrôleur ou une méthode d’action et spécifiez le nom et la valeur de l’en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="2125a-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="2125a-529">Plusieurs des interfaces de filtre ont des attributs correspondants qui peuvent être utilisés comme classes de base pour des implémentations personnalisées.</span><span class="sxs-lookup"><span data-stu-id="2125a-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="2125a-530">Les attributs de filtre :</span><span class="sxs-lookup"><span data-stu-id="2125a-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="2125a-531">Étendues de filtre et ordre d’exécution</span><span class="sxs-lookup"><span data-stu-id="2125a-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="2125a-532">Un filtre peut être ajouté au pipeline à l’une des trois *étendues*suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="2125a-533">À l’aide d’un attribut sur une action.</span><span class="sxs-lookup"><span data-stu-id="2125a-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="2125a-534">À l’aide d’un attribut sur un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="2125a-535">Dans le monde entier pour tous les contrôleurs et actions, comme indiqué dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2125a-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2125a-536">Le code précédent ajoute trois filtres globalement à l’aide de la collection [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="2125a-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="2125a-537">Ordre d’exécution par défaut</span><span class="sxs-lookup"><span data-stu-id="2125a-537">Default order of execution</span></span>

<span data-ttu-id="2125a-538">Lorsqu’il existe plusieurs filtres *du même type*, l’étendue détermine l’ordre par défaut de l’exécution du filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="2125a-539">Filtres globaux-filtres de classe surround.</span><span class="sxs-lookup"><span data-stu-id="2125a-539">Global filters surround class filters.</span></span> <span data-ttu-id="2125a-540">Les filtres de classe entourent les filtres de méthode.</span><span class="sxs-lookup"><span data-stu-id="2125a-540">Class filters surround method filters.</span></span>

<span data-ttu-id="2125a-541">En raison de l’imbrication de filtres, le code *après* des filtres s’exécute dans l’ordre inverse du code *avant*.</span><span class="sxs-lookup"><span data-stu-id="2125a-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="2125a-542">La séquence de filtre :</span><span class="sxs-lookup"><span data-stu-id="2125a-542">The filter sequence:</span></span>

* <span data-ttu-id="2125a-543">Le code *avant* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="2125a-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="2125a-544">Le code *avant* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="2125a-545">Le code *avant* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="2125a-546">Le code *après* des filtres de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="2125a-547">Le code *après* des filtres du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="2125a-548">Le code *après* des filtres globaux.</span><span class="sxs-lookup"><span data-stu-id="2125a-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="2125a-549">Voici un exemple qui illustre l’ordre dans lequel les méthodes de filtre sont appelées pour les filtres d’actions synchrones.</span><span class="sxs-lookup"><span data-stu-id="2125a-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="2125a-550">Séquence</span><span class="sxs-lookup"><span data-stu-id="2125a-550">Sequence</span></span> | <span data-ttu-id="2125a-551">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="2125a-551">Filter scope</span></span> | <span data-ttu-id="2125a-552">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="2125a-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="2125a-553">1</span><span class="sxs-lookup"><span data-stu-id="2125a-553">1</span></span> | <span data-ttu-id="2125a-554">Global</span><span class="sxs-lookup"><span data-stu-id="2125a-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2125a-555">2</span><span class="sxs-lookup"><span data-stu-id="2125a-555">2</span></span> | <span data-ttu-id="2125a-556">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="2125a-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2125a-557">3</span><span class="sxs-lookup"><span data-stu-id="2125a-557">3</span></span> | <span data-ttu-id="2125a-558">Méthode</span><span class="sxs-lookup"><span data-stu-id="2125a-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2125a-559">4</span><span class="sxs-lookup"><span data-stu-id="2125a-559">4</span></span> | <span data-ttu-id="2125a-560">Méthode</span><span class="sxs-lookup"><span data-stu-id="2125a-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2125a-561">5</span><span class="sxs-lookup"><span data-stu-id="2125a-561">5</span></span> | <span data-ttu-id="2125a-562">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="2125a-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2125a-563">6</span><span class="sxs-lookup"><span data-stu-id="2125a-563">6</span></span> | <span data-ttu-id="2125a-564">Global</span><span class="sxs-lookup"><span data-stu-id="2125a-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="2125a-565">Cette séquence montre que :</span><span class="sxs-lookup"><span data-stu-id="2125a-565">This sequence shows:</span></span>

* <span data-ttu-id="2125a-566">Le filtre de méthode est imbriqué dans le filtre de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="2125a-567">Le filtre de contrôleur est imbriqué dans le filtre global.</span><span class="sxs-lookup"><span data-stu-id="2125a-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="2125a-568">Filtres au niveau du contrôleur et de la Razor page</span><span class="sxs-lookup"><span data-stu-id="2125a-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="2125a-569">Chaque contrôleur qui hérite de la classe de base <xref:Microsoft.AspNetCore.Mvc.Controller> inclut les méthodes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) et [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="2125a-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="2125a-570">Ces méthodes :</span><span class="sxs-lookup"><span data-stu-id="2125a-570">These methods:</span></span>

* <span data-ttu-id="2125a-571">Incluent dans un wrapper les filtres qui s’exécutent pour une action donnée.</span><span class="sxs-lookup"><span data-stu-id="2125a-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="2125a-572">`OnActionExecuting` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="2125a-573">`OnActionExecuted` est appelé après tous les filtres d’actions.</span><span class="sxs-lookup"><span data-stu-id="2125a-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="2125a-574">`OnActionExecutionAsync` est appelé avant tous les filtres de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="2125a-575">Le code dans le filtre après `next` s’exécute après la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="2125a-576">Par exemple, dans l’échantillon à télécharger, `MySampleActionFilter` est appliqué globalement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2125a-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="2125a-577">`TestController` :</span><span class="sxs-lookup"><span data-stu-id="2125a-577">The `TestController`:</span></span>

* <span data-ttu-id="2125a-578">Applique le `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) à l' `FilterTest2` action.</span><span class="sxs-lookup"><span data-stu-id="2125a-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="2125a-579">Remplace `OnActionExecuting` et `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="2125a-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="2125a-580">La navigation vers `https://localhost:5001/Test/FilterTest2` exécute le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2125a-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="2125a-581">Pour les Razor pages, consultez [implémenter des filtres de Razor page en substituant des méthodes de filtre](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="2125a-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="2125a-582">Remplacement de l’ordre par défaut</span><span class="sxs-lookup"><span data-stu-id="2125a-582">Overriding the default order</span></span>

<span data-ttu-id="2125a-583">Vous pouvez remplacer la séquence d’exécution par défaut en implémentant <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="2125a-584">`IOrderedFilter` expose la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> qui est prioritaire sur l’étendue afin de déterminer l’ordre d’exécution.</span><span class="sxs-lookup"><span data-stu-id="2125a-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="2125a-585">Un filtre avec une valeur `Order` inférieure :</span><span class="sxs-lookup"><span data-stu-id="2125a-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="2125a-586">Exécute le code *avant* avant celui d’un filtre avec une valeur supérieure à `Order`.</span><span class="sxs-lookup"><span data-stu-id="2125a-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="2125a-587">Exécute le code *après* après celui d’un filtre avec une valeur `Order` supérieure.</span><span class="sxs-lookup"><span data-stu-id="2125a-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="2125a-588">La propriété `Order` peut être définie avec un paramètre de constructeur :</span><span class="sxs-lookup"><span data-stu-id="2125a-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="2125a-589">Prenez en compte les mêmes 3 filtres d’actions indiqués dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="2125a-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="2125a-590">Si la propriété `Order` du contrôleur et les filtres globaux sont définis sur 1 et 2 respectivement, l’ordre d’exécution est inversé.</span><span class="sxs-lookup"><span data-stu-id="2125a-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="2125a-591">Séquence</span><span class="sxs-lookup"><span data-stu-id="2125a-591">Sequence</span></span> | <span data-ttu-id="2125a-592">Étendue de filtre</span><span class="sxs-lookup"><span data-stu-id="2125a-592">Filter scope</span></span> | <span data-ttu-id="2125a-593">Propriété `Order`</span><span class="sxs-lookup"><span data-stu-id="2125a-593">`Order` property</span></span> | <span data-ttu-id="2125a-594">Méthode de filtre</span><span class="sxs-lookup"><span data-stu-id="2125a-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="2125a-595">1</span><span class="sxs-lookup"><span data-stu-id="2125a-595">1</span></span> | <span data-ttu-id="2125a-596">Méthode</span><span class="sxs-lookup"><span data-stu-id="2125a-596">Method</span></span> | <span data-ttu-id="2125a-597">0</span><span class="sxs-lookup"><span data-stu-id="2125a-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2125a-598">2</span><span class="sxs-lookup"><span data-stu-id="2125a-598">2</span></span> | <span data-ttu-id="2125a-599">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="2125a-599">Controller</span></span> | <span data-ttu-id="2125a-600">1</span><span class="sxs-lookup"><span data-stu-id="2125a-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="2125a-601">3</span><span class="sxs-lookup"><span data-stu-id="2125a-601">3</span></span> | <span data-ttu-id="2125a-602">Global</span><span class="sxs-lookup"><span data-stu-id="2125a-602">Global</span></span> | <span data-ttu-id="2125a-603">2</span><span class="sxs-lookup"><span data-stu-id="2125a-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="2125a-604">4</span><span class="sxs-lookup"><span data-stu-id="2125a-604">4</span></span> | <span data-ttu-id="2125a-605">Global</span><span class="sxs-lookup"><span data-stu-id="2125a-605">Global</span></span> | <span data-ttu-id="2125a-606">2</span><span class="sxs-lookup"><span data-stu-id="2125a-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="2125a-607">5</span><span class="sxs-lookup"><span data-stu-id="2125a-607">5</span></span> | <span data-ttu-id="2125a-608">Contrôleur</span><span class="sxs-lookup"><span data-stu-id="2125a-608">Controller</span></span> | <span data-ttu-id="2125a-609">1</span><span class="sxs-lookup"><span data-stu-id="2125a-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="2125a-610">6</span><span class="sxs-lookup"><span data-stu-id="2125a-610">6</span></span> | <span data-ttu-id="2125a-611">Méthode</span><span class="sxs-lookup"><span data-stu-id="2125a-611">Method</span></span> | <span data-ttu-id="2125a-612">0</span><span class="sxs-lookup"><span data-stu-id="2125a-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="2125a-613">La propriété `Order` remplace l’étendue lors de la détermination de l’ordre dans lequel les filtres s’exécutent.</span><span class="sxs-lookup"><span data-stu-id="2125a-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="2125a-614">Les filtres sont d’abord classés par ordre, puis l’étendue est utilisée pour couper les liens.</span><span class="sxs-lookup"><span data-stu-id="2125a-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="2125a-615">Tous les filtres intégrés implémentent `IOrderedFilter` et affectent 0 à la valeur `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="2125a-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="2125a-616">Pour les filtres intégrés, l’étendue détermine l’ordre, sauf si `Order` est défini sur une valeur différente de zéro.</span><span class="sxs-lookup"><span data-stu-id="2125a-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="2125a-617">Annulation et court-circuit</span><span class="sxs-lookup"><span data-stu-id="2125a-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="2125a-618">Vous pouvez court-circuiter le pipeline de filtres en définissant la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sur le paramètre <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fourni à la méthode de filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="2125a-619">Par exemple, le filtre de ressources suivant empêche l’exécution du reste du pipeline :</span><span class="sxs-lookup"><span data-stu-id="2125a-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2125a-620">Dans le code suivant, les filtres `ShortCircuitingResourceFilter` et `AddHeader` ciblent tous deux la méthode d’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="2125a-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="2125a-621">`ShortCircuitingResourceFilter` :</span><span class="sxs-lookup"><span data-stu-id="2125a-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="2125a-622">S’exécute en premier (puisqu’il s’agit d’un filtre de ressources et que `AddHeader` est un filtre d’action).</span><span class="sxs-lookup"><span data-stu-id="2125a-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="2125a-623">Court-circuite le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="2125a-624">Le filtre `AddHeader` ne s’exécute donc jamais pour l’action `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="2125a-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="2125a-625">Ce comportement est le même si les deux filtres sont appliqués au niveau de la méthode d’action, à condition que `ShortCircuitingResourceFilter` soit exécuté en premier.</span><span class="sxs-lookup"><span data-stu-id="2125a-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="2125a-626">`ShortCircuitingResourceFilter` s’exécute en premier en raison de son type de filtre ou de l’utilisation explicite de la propriété `Order`.</span><span class="sxs-lookup"><span data-stu-id="2125a-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="2125a-627">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="2125a-627">Dependency injection</span></span>

<span data-ttu-id="2125a-628">Les filtres peuvent être ajoutés par type ou par instance.</span><span class="sxs-lookup"><span data-stu-id="2125a-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="2125a-629">Si vous ajoutez une instance, celle-ci sera utilisée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="2125a-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="2125a-630">Si un type est ajouté, il est activé par type.</span><span class="sxs-lookup"><span data-stu-id="2125a-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="2125a-631">Un filtre activé par type signifie :</span><span class="sxs-lookup"><span data-stu-id="2125a-631">A type-activated filter means:</span></span>

* <span data-ttu-id="2125a-632">Une instance est créée pour chaque requête.</span><span class="sxs-lookup"><span data-stu-id="2125a-632">An instance is created for each request.</span></span>
* <span data-ttu-id="2125a-633">Les dépendances de constructeur sont remplies par [l’injection de dépendances](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="2125a-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="2125a-634">Les filtres qui sont implémentés en tant qu’attributs et ajoutés directement à des classes de contrôleur ou à de méthodes d’action ne peut pas avoir de dépendances de constructeur fournies par [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2125a-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="2125a-635">Les dépendances de constructeur ne peuvent pas être fournies par l’injection de dépendance, car :</span><span class="sxs-lookup"><span data-stu-id="2125a-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="2125a-636">Les paramètres du constructeur des attributs doivent être fournis là où ils sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="2125a-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="2125a-637">Il s’agit d’une limitation de la façon dont les attributs fonctionnent.</span><span class="sxs-lookup"><span data-stu-id="2125a-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="2125a-638">Les filtres suivants prennent en charge les dépendances de constructeur fournies à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="2125a-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="2125a-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémenté sur l’attribut.</span><span class="sxs-lookup"><span data-stu-id="2125a-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="2125a-640">Les filtres précédents peuvent être appliqués à une méthode de contrôleur ou d’action :</span><span class="sxs-lookup"><span data-stu-id="2125a-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="2125a-641">Des enregistreurs d’événements sont disponibles à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-641">Loggers are available from DI.</span></span> <span data-ttu-id="2125a-642">Toutefois, évitez la création et l’utilisation de filtres à des fins purement d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="2125a-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="2125a-643">L’[enregistrement d’infrastructure intégrée](xref:fundamentals/logging/index) fournit généralement ce qui est nécessaire pour l’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="2125a-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="2125a-644">Enregistrement ajouté aux filtres :</span><span class="sxs-lookup"><span data-stu-id="2125a-644">Logging added to filters:</span></span>

* <span data-ttu-id="2125a-645">Doit se concentrer sur les problèmes ou le comportement du domaine métier spécifique au filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="2125a-646">Ne doit **pas** enregistrer des actions ou d’autres événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="2125a-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="2125a-647">Les filtres intégrés enregistrent des actions et des événements d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="2125a-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="2125a-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2125a-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="2125a-649">Les types d’implémentation du filtre de services sont enregistrés dans `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2125a-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="2125a-650">Un <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> récupère une instance du filtre à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="2125a-651">Le code suivant affiche le `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="2125a-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2125a-652">Dans le code suivant, `AddHeaderResultServiceFilter` est ajouté au conteneur d’injection de dépendance :</span><span class="sxs-lookup"><span data-stu-id="2125a-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="2125a-653">Dans le code suivant, l’attribut `ServiceFilter` récupère une instance du filtre `AddHeaderResultServiceFilter` à partir de l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="2125a-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="2125a-654">Lors de l’utilisation de `ServiceFilterAttribute`, définir [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="2125a-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="2125a-655">Conseille que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2125a-656">Le runtime ASP.NET Core ne garantit pas :</span><span class="sxs-lookup"><span data-stu-id="2125a-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="2125a-657">Qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="2125a-658">Le filtre ne sera pas demandé à nouveau à partir du conteneur d’injection de dépendance à un stade ultérieur.</span><span class="sxs-lookup"><span data-stu-id="2125a-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="2125a-659">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="2125a-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="2125a-660">L'objet <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2125a-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2125a-661">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2125a-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2125a-662">`CreateInstance` charge le type spécifié à partir de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="2125a-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2125a-663">TypeFilterAttribute</span></span>

<span data-ttu-id="2125a-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> est similaire à <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mais son type n’est pas résolu directement à partir du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="2125a-665">Il instancie le type en utilisant <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="2125a-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="2125a-666">Étant donné que les types `TypeFilterAttribute` ne sont pas résolus directement à partir du conteneur d’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="2125a-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="2125a-667">Les types qui sont référencés avec `TypeFilterAttribute` ne doivent pas d’abord être inscrits auprès du conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="2125a-668">Leurs dépendances ne sont pas remplies par le conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="2125a-669">`TypeFilterAttribute` peut éventuellement accepter des arguments de constructeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="2125a-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="2125a-670">Lors de l’utilisation de `TypeFilterAttribute`, définir [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) :</span><span class="sxs-lookup"><span data-stu-id="2125a-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="2125a-671">Indique que l’instance de filtre *peut* être réutilisée en dehors de l’étendue de la requête dans laquelle elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2125a-672">Le runtime ASP.NET Core ne fournit aucune garantie qu’une seule instance du filtre sera créée.</span><span class="sxs-lookup"><span data-stu-id="2125a-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="2125a-673">Évitez de l’utiliser avec un filtre qui dépend de services avec une durée de vie autre que singleton.</span><span class="sxs-lookup"><span data-stu-id="2125a-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="2125a-674">L’exemple suivant indique comment passer des arguments vers un type en utilisant `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="2125a-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="2125a-675">Filtres d’autorisations</span><span class="sxs-lookup"><span data-stu-id="2125a-675">Authorization filters</span></span>

<span data-ttu-id="2125a-676">Filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="2125a-676">Authorization filters:</span></span>

* <span data-ttu-id="2125a-677">Sont les premiers filtres à être exécutés dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="2125a-678">Contrôlent l’accès aux méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-678">Control access to action methods.</span></span>
* <span data-ttu-id="2125a-679">Ont une méthode avant, mais pas de méthode après.</span><span class="sxs-lookup"><span data-stu-id="2125a-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="2125a-680">Les filtres d’autorisations personnalisés nécessitent une infrastructure d’autorisation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="2125a-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="2125a-681">Préférez la configuration des stratégies d’autorisation ou l’écriture d’une stratégie d’autorisation personnalisée à l’écriture d’un filtre personnalisé.</span><span class="sxs-lookup"><span data-stu-id="2125a-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="2125a-682">Le filtre d'autorisations intégré :</span><span class="sxs-lookup"><span data-stu-id="2125a-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="2125a-683">Appelle le système d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="2125a-683">Calls the authorization system.</span></span>
* <span data-ttu-id="2125a-684">N’autoriser les requêtes.</span><span class="sxs-lookup"><span data-stu-id="2125a-684">Does not authorize requests.</span></span>

<span data-ttu-id="2125a-685">Ne levez **pas** d’exceptions dans les filtres d’autorisations :</span><span class="sxs-lookup"><span data-stu-id="2125a-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="2125a-686">L’exception ne sera pas gérée.</span><span class="sxs-lookup"><span data-stu-id="2125a-686">The exception will not be handled.</span></span>
* <span data-ttu-id="2125a-687">Les filtres d’exceptions ne gèreront pas l’exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="2125a-688">Songez à émettre une stimulation quand un filtre d’autorisations se produit.</span><span class="sxs-lookup"><span data-stu-id="2125a-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="2125a-689">Découvrez plus d’informations sur [l’autorisation](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="2125a-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="2125a-690">Filtres de ressources</span><span class="sxs-lookup"><span data-stu-id="2125a-690">Resource filters</span></span>

<span data-ttu-id="2125a-691">Filtres de ressources :</span><span class="sxs-lookup"><span data-stu-id="2125a-691">Resource filters:</span></span>

* <span data-ttu-id="2125a-692">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="2125a-693">L’exécution inclut dans un wrapper la majeure partie du pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="2125a-694">Seuls les [filtres d’autorisations](#authorization-filters) s’exécutent avant les filtres de ressources.</span><span class="sxs-lookup"><span data-stu-id="2125a-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="2125a-695">Les filtres de ressources sont utiles pour court-circuiter la majeure partie du pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="2125a-696">Par exemple, un filtre de mise en cache peut éviter le reste du pipeline dans une correspondance dans le cache.</span><span class="sxs-lookup"><span data-stu-id="2125a-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="2125a-697">Exemples de filtre de ressources :</span><span class="sxs-lookup"><span data-stu-id="2125a-697">Resource filter examples:</span></span>

* <span data-ttu-id="2125a-698">[Le filtre de ressources de court-circuit](#short-circuiting-resource-filter) illustré précédemment.</span><span class="sxs-lookup"><span data-stu-id="2125a-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="2125a-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) :</span><span class="sxs-lookup"><span data-stu-id="2125a-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="2125a-700">Il empêche la liaison de données d’accéder aux données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="2125a-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="2125a-701">Il est utilisé pour les chargements de fichiers volumineux et pour empêcher que le formulaire de données ne soit lu en mémoire.</span><span class="sxs-lookup"><span data-stu-id="2125a-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="2125a-702">Filtres d’actions</span><span class="sxs-lookup"><span data-stu-id="2125a-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2125a-703">Les filtres d’action ne s’appliquent **pas** aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="2125a-703">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="2125a-704">Les pages prennent en charge <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="2125a-704"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="2125a-705">Pour plus d’informations, consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2125a-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="2125a-706">Filtres d'actions :</span><span class="sxs-lookup"><span data-stu-id="2125a-706">Action filters:</span></span>

* <span data-ttu-id="2125a-707">Implémentez l’interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="2125a-708">Leur exécution entoure l’exécution de méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="2125a-709">Le code suivant montre un exemple de filtre d’actions :</span><span class="sxs-lookup"><span data-stu-id="2125a-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2125a-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fournit les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="2125a-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> : permet de lire les entrées vers une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="2125a-712"><xref:Microsoft.AspNetCore.Mvc.Controller> : permet de manipuler l’instance de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="2125a-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="2125a-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> : la définition de `Result` court-circuite l’exécution de la méthode d’action et les filtres d’actions suivants.</span><span class="sxs-lookup"><span data-stu-id="2125a-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="2125a-714">Levée d’une exception dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="2125a-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="2125a-715">Empêche l’exécution des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="2125a-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="2125a-716">Contrairement au paramètre `Result`, est traité comme un échec plutôt que comme un résultat positif.</span><span class="sxs-lookup"><span data-stu-id="2125a-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2125a-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fournit `Controller` et `Result`, en plus des propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="2125a-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="2125a-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> : sa valeur est true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2125a-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> : sa valeur est non Null si l’action ou un filtre d’actions précédemment exécuté a lancé une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="2125a-720">Paramètre de cette propriété sur null :</span><span class="sxs-lookup"><span data-stu-id="2125a-720">Setting this property to null:</span></span>

  * <span data-ttu-id="2125a-721">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="2125a-722">`Result` est exécuté comme s’il avait été retourné à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="2125a-723">Pour un `IAsyncActionFilter`, un appel à <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="2125a-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="2125a-724">Exécute tous les filtres d’actions suivants et la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="2125a-725">Retourne `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="2125a-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="2125a-726">Pour court-circuiter, attribuez <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> à une instance de résultat et n’appelez pas le `next` (le `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="2125a-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="2125a-727">L’infrastructure fournit un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="2125a-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="2125a-728">Le filtre d’actions `OnActionExecuting` peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="2125a-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="2125a-729">Valider l’état du modèle.</span><span class="sxs-lookup"><span data-stu-id="2125a-729">Validate model state.</span></span>
* <span data-ttu-id="2125a-730">Renvoyer une erreur si l’état n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="2125a-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="2125a-731">La méthode `OnActionExecuted` s’exécute après la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="2125a-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="2125a-732">Et peut voir et manipuler les résultats de l’action via la propriété <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="2125a-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="2125a-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> est défini sur true si l’exécution de l’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2125a-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> est défini sur une valeur non Null si l’action ou un filtre d’actions suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="2125a-735">Le fait de définir `Exception` avec la valeur null :</span><span class="sxs-lookup"><span data-stu-id="2125a-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="2125a-736">Gère effectivement une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="2125a-737">`ActionExecutedContext.Result` est exécuté comme s’il avait été retourné normalement à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="2125a-738">Filtres d’exceptions</span><span class="sxs-lookup"><span data-stu-id="2125a-738">Exception filters</span></span>

<span data-ttu-id="2125a-739">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="2125a-739">Exception filters:</span></span>

* <span data-ttu-id="2125a-740">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2125a-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="2125a-741">Ils peuvent être utilisés pour implémenter des stratégies de gestion des erreurs communes.</span><span class="sxs-lookup"><span data-stu-id="2125a-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="2125a-742">L’échantillon de filtre d’exceptions suivant utilise un affichage personnalisé des erreurs pour afficher des détails sur les exceptions qui se produisent pendant que l’application est en développement :</span><span class="sxs-lookup"><span data-stu-id="2125a-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="2125a-743">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="2125a-743">Exception filters:</span></span>

* <span data-ttu-id="2125a-744">N’ont pas d’événements avant et après.</span><span class="sxs-lookup"><span data-stu-id="2125a-744">Don't have before and after events.</span></span>
* <span data-ttu-id="2125a-745">Implémentent <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2125a-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="2125a-746">Gérer les exceptions non gérées qui se produisent lors de la création d’une Razor page ou d’un contrôleur, la [liaison de modèle](xref:mvc/models/model-binding), les filtres d’action ou les méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="2125a-747">N’interceptent **pas** les exceptions qui se produisent dans l’exécution des filtres de ressources, des filtres de résultats ou des résultats MVC.</span><span class="sxs-lookup"><span data-stu-id="2125a-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="2125a-748">Pour gérer une exception, définissez la propriété <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> sur `true` ou écrivez une réponse.</span><span class="sxs-lookup"><span data-stu-id="2125a-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="2125a-749">Ceci arrête la propagation de l’exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-749">This stops propagation of the exception.</span></span> <span data-ttu-id="2125a-750">Un filtre d’exceptions ne peut pas changer une exception en « réussite ».</span><span class="sxs-lookup"><span data-stu-id="2125a-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="2125a-751">Seul un filtre d’actions peut le faire.</span><span class="sxs-lookup"><span data-stu-id="2125a-751">Only an action filter can do that.</span></span>

<span data-ttu-id="2125a-752">Les filtres d’exceptions :</span><span class="sxs-lookup"><span data-stu-id="2125a-752">Exception filters:</span></span>

* <span data-ttu-id="2125a-753">Conviennent pour intercepter des exceptions qui se produisent dans des actions.</span><span class="sxs-lookup"><span data-stu-id="2125a-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="2125a-754">N’offrent pas la même souplesse que le middleware (intergiciel) de gestion des erreurs.</span><span class="sxs-lookup"><span data-stu-id="2125a-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="2125a-755">Privilégiez le middleware pour la gestion des exceptions.</span><span class="sxs-lookup"><span data-stu-id="2125a-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="2125a-756">Utilisez les filtres d’exceptions uniquement lorsque la gestion des erreurs *diffère* selon la méthode d’action appelée.</span><span class="sxs-lookup"><span data-stu-id="2125a-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="2125a-757">Par exemple, votre application peut avoir des méthodes d’action à la fois pour des points de terminaison d’API et pour des affichages/HTML.</span><span class="sxs-lookup"><span data-stu-id="2125a-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="2125a-758">Les points de terminaison d’API peuvent retourner des informations d’erreur au format JSON, tandis que les actions basées sur une vue peuvent retourner une page d’erreur au format HTML.</span><span class="sxs-lookup"><span data-stu-id="2125a-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="2125a-759">Filtres de résultats</span><span class="sxs-lookup"><span data-stu-id="2125a-759">Result filters</span></span>

<span data-ttu-id="2125a-760">Filtres de résultats :</span><span class="sxs-lookup"><span data-stu-id="2125a-760">Result filters:</span></span>

* <span data-ttu-id="2125a-761">Implémenter une interface :</span><span class="sxs-lookup"><span data-stu-id="2125a-761">Implement an interface:</span></span>
  * <span data-ttu-id="2125a-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2125a-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="2125a-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="2125a-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="2125a-764">Leur exécution entoure l’exécution de résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="2125a-765">IResultFilter et IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="2125a-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="2125a-766">Le code suivant indique un filtre de résultats qui ajoute un en-tête HTTP :</span><span class="sxs-lookup"><span data-stu-id="2125a-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2125a-767">Le type de résultat à exécuter dépend de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="2125a-768">Une action retournant un affichage inclut tous les traitements Razor dans le cadre de la <xref:Microsoft.AspNetCore.Mvc.ViewResult> à exécuter.</span><span class="sxs-lookup"><span data-stu-id="2125a-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="2125a-769">Une méthode d’API peut effectuer une sérialisation dans le cadre de l’exécution du résultat.</span><span class="sxs-lookup"><span data-stu-id="2125a-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="2125a-770">En savoir plus sur les [résultats des actions](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="2125a-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="2125a-771">Les filtres de résultats ne sont exécutés que lorsqu’une action ou un filtre d’action produit un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="2125a-772">Les filtres de résultats ne sont pas exécutés dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="2125a-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="2125a-773">Un filtre d’autorisation ou des courts-circuits de filtre de ressources le pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="2125a-774">Un filtre d’exception gère une exception en générant un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="2125a-775">La méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> peut court-circuiter l’exécution du résultat d’action et les filtres de résultats suivants en définissant <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="2125a-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="2125a-776">Écrivez dans l’objet de réponse quand vous court-circuitez pour éviter de générer une réponse vide.</span><span class="sxs-lookup"><span data-stu-id="2125a-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="2125a-777">La levée d’une exception dans `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="2125a-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="2125a-778">Empêche l’exécution du résultat d’action et des filtres suivants.</span><span class="sxs-lookup"><span data-stu-id="2125a-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="2125a-779">Est traitée comme une erreur et non comme une réussite.</span><span class="sxs-lookup"><span data-stu-id="2125a-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2125a-780">Lorsque la <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> méthode s’exécute, la réponse a probablement déjà été envoyée au client.</span><span class="sxs-lookup"><span data-stu-id="2125a-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="2125a-781">Si la réponse a déjà été envoyée au client, elle ne peut plus être modifiée.</span><span class="sxs-lookup"><span data-stu-id="2125a-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="2125a-782">`ResultExecutedContext.Canceled` est défini sur `true` si l’exécution du résultat d’action a été court-circuitée par un autre filtre.</span><span class="sxs-lookup"><span data-stu-id="2125a-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="2125a-783">`ResultExecutedContext.Exception` est défini sur une valeur non Null si le résultat d’action ou un filtre de résultats suivant a levé une exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="2125a-784">Le paramètre de `Exception` sur Null gère effectivement une exception et évite à l’exception d’être à nouveau levée par ASP.NET Core plus loin dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="2125a-785">Il n’existe aucun moyen fiable pour écrire des données dans une réponse lors de la gestion d’une exception dans un filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="2125a-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="2125a-786">Si les en-têtes ont été vidés pour le client lorsqu’un résultat d’action lance une exception, il n’existe aucun mécanisme fiable pour envoyer un code d’échec.</span><span class="sxs-lookup"><span data-stu-id="2125a-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="2125a-787">Pour un <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, un appel à `await next` sur le <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> exécute tous les filtres de résultats suivants et le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="2125a-788">Pour court-circuiter, définissez [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) sur `true` et n’appelez pas `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="2125a-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="2125a-789">L’infrastructure fournit un `ResultFilterAttribute` abstrait que vous pouvez placer dans une sous-classe.</span><span class="sxs-lookup"><span data-stu-id="2125a-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="2125a-790">La classe [AddHeaderAttribute](#add-header-attribute) ci-dessus est un exemple d’un attribut de filtre de résultats.</span><span class="sxs-lookup"><span data-stu-id="2125a-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="2125a-791">IAlwaysRunResultFilter et IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="2125a-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="2125a-792">Les interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> et <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> déclarent une implémentation <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> qui s’exécute pour tous les résultats d’action.</span><span class="sxs-lookup"><span data-stu-id="2125a-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="2125a-793">Cela comprend les résultats d’action produits par :</span><span class="sxs-lookup"><span data-stu-id="2125a-793">This includes action results produced by:</span></span>

* <span data-ttu-id="2125a-794">Filtres d’autorisation et filtres de ressources qui court-circuitent.</span><span class="sxs-lookup"><span data-stu-id="2125a-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="2125a-795">Filtres d’exception.</span><span class="sxs-lookup"><span data-stu-id="2125a-795">Exception filters.</span></span>

<span data-ttu-id="2125a-796">Par exemple, le filtre suivant exécute et définit toujours un résultat d’action (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) avec un code d’état *422 Entité non traitée* en cas d’échec de la négociation de contenu :</span><span class="sxs-lookup"><span data-stu-id="2125a-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="2125a-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2125a-797">IFilterFactory</span></span>

<span data-ttu-id="2125a-798">L'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2125a-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="2125a-799">Par conséquent, une instance de `IFilterFactory` peut être utilisée comme instance de `IFilterMetadata` n’importe où dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="2125a-800">Quan se prépare à appeler le filtre, il tente de le caster en `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="2125a-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="2125a-801">Si ce cast réussit, la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> est appelée pour créer l’instance `IFilterMetadata` qui sera appelée.</span><span class="sxs-lookup"><span data-stu-id="2125a-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="2125a-802">La conception est flexible, car il n’est pas nécessaire de définir explicitement le pipeline de filtres exact quand l’application démarre.</span><span class="sxs-lookup"><span data-stu-id="2125a-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="2125a-803">Une autre approche pour la création de filtres est d’implémenter `IFilterFactory` à l’aide des implémentations d’attribut personnalisé :</span><span class="sxs-lookup"><span data-stu-id="2125a-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="2125a-804">Le code précédent peut être testé en exécutant l’[échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) :</span><span class="sxs-lookup"><span data-stu-id="2125a-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="2125a-805">Appeler les outils de développement F12.</span><span class="sxs-lookup"><span data-stu-id="2125a-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="2125a-806">Accédez à `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="2125a-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="2125a-807">Les outils de développement F12 affichent les en-têtes de réponse suivants ajoutés par l’exemple de code :</span><span class="sxs-lookup"><span data-stu-id="2125a-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="2125a-808">**Auteur :**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="2125a-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="2125a-809">**globaladdheader :** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="2125a-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="2125a-810">**interne :**`My header`</span><span class="sxs-lookup"><span data-stu-id="2125a-810">**internal:** `My header`</span></span>

<span data-ttu-id="2125a-811">Le code précédent crée l’en-tête de réponse **interne :** `My header`.</span><span class="sxs-lookup"><span data-stu-id="2125a-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="2125a-812">IFilterFactory implémenté sur un attribut</span><span class="sxs-lookup"><span data-stu-id="2125a-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="2125a-813">Les filtres qui implémentent `IFilterFactory` sont utiles pour les filtres qui :</span><span class="sxs-lookup"><span data-stu-id="2125a-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="2125a-814">Ne nécessitent pas le passage de paramètres.</span><span class="sxs-lookup"><span data-stu-id="2125a-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="2125a-815">Disposent de dépendances de constructeur qui doivent être remplies par l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="2125a-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="2125a-816">L'objet <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implémente l'objet <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2125a-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2125a-817">`IFilterFactory` expose la méthode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> pour la création d’une instance <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2125a-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2125a-818">`CreateInstance` charge le type spécifié à partir du conteneur de services (injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="2125a-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="2125a-819">Le code suivant illustre trois approches pour appliquer `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="2125a-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2125a-820">Dans le code précédent, la décoration de la méthode avec `[SampleActionFilter]` est la meilleure approche pour appliquer `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="2125a-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="2125a-821">Utilisation d’un intergiciel dans le pipeline de filtres</span><span class="sxs-lookup"><span data-stu-id="2125a-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="2125a-822">Les filtres de ressources fonctionnent comme un [intergiciel](xref:fundamentals/middleware/index) dans la mesure où ils entourent l’exécution de tout ce qui vient plus tard dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="2125a-823">Les filtres diffèrent cependant d’un intergiciel dans la mesure où ils font partie du runtime ASP.NET Core, ce qui signifie qu’ils ont accès au contexte et aux constructions ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2125a-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="2125a-824">Pour utiliser un intergiciel comme filtre, créez un type avec une méthode `Configure` qui spécifie l’intergiciel que vous voulez injecter dans le pipeline de filtres.</span><span class="sxs-lookup"><span data-stu-id="2125a-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="2125a-825">Voici un exemple qui utilise l’intergiciel de localisation pour établir la culture actuelle d’une requête :</span><span class="sxs-lookup"><span data-stu-id="2125a-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="2125a-826">Utilisez le <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pour exécuter l’intergiciel :</span><span class="sxs-lookup"><span data-stu-id="2125a-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="2125a-827">Les filtres d’intergiciels s’exécutent à la même étape du pipeline de filtres en tant que filtres de ressources, avant la liaison de modèle et après le reste du pipeline.</span><span class="sxs-lookup"><span data-stu-id="2125a-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="2125a-828">Actions suivantes</span><span class="sxs-lookup"><span data-stu-id="2125a-828">Next actions</span></span>

* <span data-ttu-id="2125a-829">Consultez [méthodes de filtre pour les Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2125a-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="2125a-830">Pour expérimenter les filtres, [téléchargez, testez et modifiez l’échantillon Github](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="2125a-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
