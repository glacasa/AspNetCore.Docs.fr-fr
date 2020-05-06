---
title: Méthodes de filtre Razor pour les Pages dans ASP.net Core
author: Rick-Anderson
description: Découvrez comment créer des méthodes de filtre Razor pour les Pages dans ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/filter
ms.openlocfilehash: 68962d5a3a49e52510d72899e7dead2c1983d8b6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775516"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="ca925-103">Méthodes de filtre Razor pour les Pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ca925-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca925-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca925-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="ca925-105">Filtres de page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent Razor aux pages d’exécuter du code avant Razor et après l’exécution d’un gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="ca925-105"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="ca925-106">Les filtres de page sont similaires aux [filtres d’action MVC ASP.net Core](xref:mvc/controllers/filters#action-filters), sauf qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de page individuelles.</span><span class="sxs-lookup"><span data-stu-id="ca925-106"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="ca925-107">Filtres de page :</span><span class="sxs-lookup"><span data-stu-id="ca925-107"> Page filters:</span></span>

* <span data-ttu-id="ca925-108">Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ca925-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="ca925-109">Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ca925-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="ca925-110">Exécutent le code après l’exécution de la méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="ca925-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="ca925-111">Peuvent être implémentés dans une page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="ca925-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="ca925-112">Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca925-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="ca925-113">Peut avoir des dépendances de constructeur remplies par [injection de dépendance](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="ca925-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ca925-114">Pour plus d’informations, consultez [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) et [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="ca925-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="ca925-115">Alors que les constructeurs de page et l’intergiciel (middleware) activent l’exécution de code personnalisé avant Razor l’exécution d’une méthode <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> de gestionnaire, seuls les filtres de page permettent l’accès à et à la page.</span><span class="sxs-lookup"><span data-stu-id="ca925-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="ca925-116">L' `HttpContext`intergiciel a accès à, mais pas au « contexte de page ».</span><span class="sxs-lookup"><span data-stu-id="ca925-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="ca925-117">Les filtres ont <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> un paramètre dérivé, qui fournit l' `HttpContext`accès à.</span><span class="sxs-lookup"><span data-stu-id="ca925-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="ca925-118">Par exemple, l’exemple [Implémenter un attribut de filtre](#ifa) ajoute un en-tête à la réponse, ce qu’il est impossible de faire avec des constructeurs ou des middlewares.</span><span class="sxs-lookup"><span data-stu-id="ca925-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="ca925-119">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca925-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="ca925-120">Les filtres de page fournissent les méthodes suivantes, qui peuvent être appliquées globalement ou au niveau de la page :</span><span class="sxs-lookup"><span data-stu-id="ca925-120"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="ca925-121">Méthodes synchrones :</span><span class="sxs-lookup"><span data-stu-id="ca925-121">Synchronous methods:</span></span>

  * <span data-ttu-id="ca925-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ca925-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ca925-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ca925-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="ca925-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="ca925-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="ca925-125">Méthodes asynchrones :</span><span class="sxs-lookup"><span data-stu-id="ca925-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="ca925-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ca925-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ca925-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.</span><span class="sxs-lookup"><span data-stu-id="ca925-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="ca925-128">Implémentez la version synchrone ou asynchrone d’une interface de filtre, mais **pas** **les deux** .</span><span class="sxs-lookup"><span data-stu-id="ca925-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ca925-129">Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="ca925-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ca925-130">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="ca925-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ca925-131">Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées.</span><span class="sxs-lookup"><span data-stu-id="ca925-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="ca925-132">La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="ca925-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="ca925-133">Implémenter Razor des filtres de page globalement</span><span class="sxs-lookup"><span data-stu-id="ca925-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="ca925-134">Le code suivant implémente `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ca925-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="ca925-135">Dans le code précédent, `ProcessUserAgent.Write` est le code fourni par l’utilisateur qui fonctionne avec la chaîne de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca925-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="ca925-136">Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ca925-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="ca925-137">Le code suivant appelle <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour appliquer à `SampleAsyncPageFilter` uniquement les pages dans */movies*:</span><span class="sxs-lookup"><span data-stu-id="ca925-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="ca925-138">Le code suivant implémente le filtre `IPageFilter` synchrone :</span><span class="sxs-lookup"><span data-stu-id="ca925-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="ca925-139">Le code suivant active le filtre `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ca925-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="ca925-140">Implémenter Razor des filtres de page en substituant des méthodes de filtre</span><span class="sxs-lookup"><span data-stu-id="ca925-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="ca925-141">Le code suivant remplace les filtres de page Razor asynchrones :</span><span class="sxs-lookup"><span data-stu-id="ca925-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="ca925-142">Implémenter un attribut de filtre</span><span class="sxs-lookup"><span data-stu-id="ca925-142">Implement a filter attribute</span></span>

<span data-ttu-id="ca925-143">Le filtre de filtre <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> basé sur les attributs intégré peut être sous-classé.</span><span class="sxs-lookup"><span data-stu-id="ca925-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="ca925-144">Le filtre suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="ca925-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="ca925-145">Le code suivant applique l’attribut `AddHeader` :</span><span class="sxs-lookup"><span data-stu-id="ca925-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="ca925-146">Utilisez un outil tel que les outils de développement du navigateur pour examiner les en-têtes.</span><span class="sxs-lookup"><span data-stu-id="ca925-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="ca925-147">Sous **en-têtes**de `author: Rick` réponse, est affiché.</span><span class="sxs-lookup"><span data-stu-id="ca925-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="ca925-148">Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="ca925-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="ca925-149">Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="ca925-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="ca925-150">Attribut de filtre Authorize</span><span class="sxs-lookup"><span data-stu-id="ca925-150">Authorize filter attribute</span></span>

<span data-ttu-id="ca925-151">L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ca925-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca925-152">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca925-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="ca925-153">Filtres de page [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) et [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) permettent Razor aux pages d’exécuter du code avant Razor et après l’exécution d’un gestionnaire de page.</span><span class="sxs-lookup"><span data-stu-id="ca925-153"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="ca925-154">Les filtres de page sont similaires aux [filtres d’action MVC ASP.net Core](xref:mvc/controllers/filters#action-filters), sauf qu’ils ne peuvent pas être appliqués aux méthodes de gestionnaire de page individuelles.</span><span class="sxs-lookup"><span data-stu-id="ca925-154"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="ca925-155">Filtres de page :</span><span class="sxs-lookup"><span data-stu-id="ca925-155"> Page filters:</span></span>

* <span data-ttu-id="ca925-156">Exécutent le code après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ca925-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="ca925-157">Exécutent le code avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ca925-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="ca925-158">Exécutent le code après l’exécution de la méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="ca925-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="ca925-159">Peuvent être implémentés dans une page ou globalement.</span><span class="sxs-lookup"><span data-stu-id="ca925-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="ca925-160">Ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="ca925-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="ca925-161">Le code peut être exécuté avant l’exécution d’une méthode de gestionnaire à l’aide du constructeur de page Razor ou de l’intergiciel (middleware), mais seuls les filtres de page ont accès à [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="ca925-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="ca925-162">Les filtres ont un paramètre dérivé de [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) qui fournit un accès à `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="ca925-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="ca925-163">Par exemple, l’exemple [Implémenter un attribut de filtre](#ifa) ajoute un en-tête à la réponse, ce qu’il est impossible de faire avec des constructeurs ou des middlewares.</span><span class="sxs-lookup"><span data-stu-id="ca925-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="ca925-164">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca925-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="ca925-165">Les filtres de page fournissent les méthodes suivantes, qui peuvent être appliquées globalement ou au niveau de la page :</span><span class="sxs-lookup"><span data-stu-id="ca925-165"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="ca925-166">Méthodes synchrones :</span><span class="sxs-lookup"><span data-stu-id="ca925-166">Synchronous methods:</span></span>

  * <span data-ttu-id="ca925-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : appelée après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ca925-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ca925-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, une fois la liaison de données terminée.</span><span class="sxs-lookup"><span data-stu-id="ca925-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="ca925-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : appelée avant l’exécution de la méthode de gestionnaire, avant le résultat de l’action.</span><span class="sxs-lookup"><span data-stu-id="ca925-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="ca925-170">Méthodes asynchrones :</span><span class="sxs-lookup"><span data-stu-id="ca925-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="ca925-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : appelée de manière asynchrone après la sélection d’une méthode de gestionnaire, mais avant la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="ca925-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="ca925-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : appelée de manière asynchrone avant l’appel de la méthode de gestionnaire, une fois la liaison de modèle terminée.</span><span class="sxs-lookup"><span data-stu-id="ca925-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="ca925-173">Implémentez la version synchrone **ou bien** la version asynchrone d’une interface de filtre, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="ca925-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="ca925-174">Le framework vérifie d’abord si le filtre implémente l’interface asynchrone et, le cas échéant, il appelle cette interface.</span><span class="sxs-lookup"><span data-stu-id="ca925-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ca925-175">Dans le cas contraire, il appelle la ou les méthodes de l’interface synchrone.</span><span class="sxs-lookup"><span data-stu-id="ca925-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ca925-176">Si les deux interfaces sont implémentées, seules les méthodes Async sont appelées.</span><span class="sxs-lookup"><span data-stu-id="ca925-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="ca925-177">La même règle s’applique aux substitutions dans les pages : implémentez la version synchrone ou asynchrone de la substitution, mais pas les deux.</span><span class="sxs-lookup"><span data-stu-id="ca925-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="ca925-178">Implémenter Razor des filtres de page globalement</span><span class="sxs-lookup"><span data-stu-id="ca925-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="ca925-179">Le code suivant implémente `IAsyncPageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ca925-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="ca925-180">Dans le code précédent, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="ca925-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="ca925-181">Il est utilisé dans l’exemple pour fournir des informations de trace pour l’application.</span><span class="sxs-lookup"><span data-stu-id="ca925-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="ca925-182">Le code suivant active le filtre `SampleAsyncPageFilter` dans la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ca925-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="ca925-183">Le code suivant montre l’intégralité de la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ca925-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="ca925-184">Le code suivant appelle `AddFolderApplicationModelConvention` pour appliquer le filtre `SampleAsyncPageFilter` uniquement aux pages dans */subFolder* :</span><span class="sxs-lookup"><span data-stu-id="ca925-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="ca925-185">Le code suivant implémente le filtre `IPageFilter` synchrone :</span><span class="sxs-lookup"><span data-stu-id="ca925-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="ca925-186">Le code suivant active le filtre `SamplePageFilter` :</span><span class="sxs-lookup"><span data-stu-id="ca925-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="ca925-187">Implémenter Razor des filtres de page en substituant des méthodes de filtre</span><span class="sxs-lookup"><span data-stu-id="ca925-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="ca925-188">Le code suivant remplace les filtres de page Razor synchrones :</span><span class="sxs-lookup"><span data-stu-id="ca925-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="ca925-189">Implémenter un attribut de filtre</span><span class="sxs-lookup"><span data-stu-id="ca925-189">Implement a filter attribute</span></span>

<span data-ttu-id="ca925-190">Le filtre intégré [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) basé sur un attribut peut être sous-classé.</span><span class="sxs-lookup"><span data-stu-id="ca925-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="ca925-191">Le filtre suivant ajoute un en-tête à la réponse :</span><span class="sxs-lookup"><span data-stu-id="ca925-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="ca925-192">Le code suivant applique l’attribut `AddHeader` :</span><span class="sxs-lookup"><span data-stu-id="ca925-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="ca925-193">Pour obtenir des instructions sur le remplacement de l’ordre, consultez [Remplacement de l’ordre par défaut](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="ca925-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="ca925-194">Pour obtenir des instructions sur le court-circuitage du pipeline de filtres à partir d’un filtre, consultez [Annulation et court-circuit](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="ca925-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="ca925-195">Attribut de filtre Authorize</span><span class="sxs-lookup"><span data-stu-id="ca925-195">Authorize filter attribute</span></span>

<span data-ttu-id="ca925-196">L’attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) peut être appliqué à `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ca925-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
