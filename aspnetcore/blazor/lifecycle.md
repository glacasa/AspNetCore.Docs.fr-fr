---
title: BlazorCycle de vie ASP.net Core
author: guardrex
description: Découvrez comment utiliser les Razor méthodes de cycle de vie des composants dans les Blazor applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e4fcd86b6e6a84d9e34a83688f9fb80c6907e5f3
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438913"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="ed403-103">BlazorCycle de vie ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ed403-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="ed403-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ed403-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ed403-105">L' Blazor infrastructure comprend des méthodes de cycle de vie synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="ed403-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="ed403-106">Substituez les méthodes de cycle de vie pour effectuer des opérations supplémentaires sur les composants lors de l’initialisation et du rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="ed403-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="ed403-107">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="ed403-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="ed403-108">Méthodes d’initialisation de composant</span><span class="sxs-lookup"><span data-stu-id="ed403-108">Component initialization methods</span></span>

<span data-ttu-id="ed403-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> sont appelés lorsque le composant est initialisé après avoir reçu ses paramètres initiaux de son composant parent.</span><span class="sxs-lookup"><span data-stu-id="ed403-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="ed403-110">Utilisez `OnInitializedAsync` lorsque le composant exécute une opération asynchrone et doit être actualisé lorsque l’opération est terminée.</span><span class="sxs-lookup"><span data-stu-id="ed403-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="ed403-111">Pour une opération synchrone, remplacez `OnInitialized` :</span><span class="sxs-lookup"><span data-stu-id="ed403-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="ed403-112">Pour effectuer une opération asynchrone, substituez `OnInitializedAsync` et utilisez le `await` mot clé sur l’opération :</span><span class="sxs-lookup"><span data-stu-id="ed403-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="ed403-113">Applications serveur qui [préaffichent le contenu](xref:blazor/hosting-model-configuration#render-mode) de l’appel à `OnInitializedAsync` **_deux reprises_**:</span><span class="sxs-lookup"><span data-stu-id="ed403-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="ed403-114">Une fois lorsque le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="ed403-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="ed403-115">Une deuxième fois lorsque le navigateur établit une connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="ed403-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="ed403-116">Pour empêcher `OnInitializedAsync` l’exécution à deux reprises du code du développeur, consultez la section [reconnexion avec état après le prérendu](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="ed403-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="ed403-117">Pendant Blazor le prérendu d’une application serveur, certaines actions, telles que l’appel de JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie.</span><span class="sxs-lookup"><span data-stu-id="ed403-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="ed403-118">Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus.</span><span class="sxs-lookup"><span data-stu-id="ed403-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="ed403-119">Pour plus d’informations, consultez la section [détecter quand l’application est un prérendu](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="ed403-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="ed403-120">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="ed403-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ed403-121">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ed403-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="ed403-122">Les paramètres Before sont définis</span><span class="sxs-lookup"><span data-stu-id="ed403-122">Before parameters are set</span></span>

<span data-ttu-id="ed403-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>définit les paramètres fournis par le parent du composant dans l’arborescence de rendu :</span><span class="sxs-lookup"><span data-stu-id="ed403-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="ed403-124"><xref:Microsoft.AspNetCore.Components.ParameterView>contient l’ensemble des valeurs de paramètre à chaque fois que `SetParametersAsync` est appelé.</span><span class="sxs-lookup"><span data-stu-id="ed403-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="ed403-125">L’implémentation par défaut de `SetParametersAsync` définit la valeur de chaque propriété avec l' `[Parameter]` attribut ou ayant `[CascadingParameter]` une valeur correspondante dans le `ParameterView` .</span><span class="sxs-lookup"><span data-stu-id="ed403-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="ed403-126">Les paramètres qui n’ont pas de valeur correspondante dans `ParameterView` ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="ed403-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="ed403-127">Si `base.SetParametersAync` n’est pas appelé, le code personnalisé peut interpréter la valeur des paramètres entrants de la façon requise.</span><span class="sxs-lookup"><span data-stu-id="ed403-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="ed403-128">Par exemple, il n’est pas nécessaire d’assigner les paramètres entrants aux propriétés de la classe.</span><span class="sxs-lookup"><span data-stu-id="ed403-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="ed403-129">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="ed403-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ed403-130">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ed403-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="ed403-131">Une fois les paramètres définis</span><span class="sxs-lookup"><span data-stu-id="ed403-131">After parameters are set</span></span>

<span data-ttu-id="ed403-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> sont appelées :</span><span class="sxs-lookup"><span data-stu-id="ed403-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="ed403-133">Lorsque le composant est initialisé et a reçu son premier jeu de paramètres de son composant parent.</span><span class="sxs-lookup"><span data-stu-id="ed403-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="ed403-134">Lorsque le composant parent est à nouveau rendu et fournit :</span><span class="sxs-lookup"><span data-stu-id="ed403-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="ed403-135">Seuls les types immuables primitifs connus dont au moins un paramètre a été modifié.</span><span class="sxs-lookup"><span data-stu-id="ed403-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="ed403-136">Tous les paramètres de type complexe.</span><span class="sxs-lookup"><span data-stu-id="ed403-136">Any complex-typed parameters.</span></span> <span data-ttu-id="ed403-137">L’infrastructure ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, donc elle traite le jeu de paramètres comme étant modifié.</span><span class="sxs-lookup"><span data-stu-id="ed403-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="ed403-138">Un travail asynchrone lors de l’application de paramètres et de valeurs de propriété doit se produire pendant l' `OnParametersSetAsync` événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="ed403-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="ed403-139">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="ed403-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ed403-140">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ed403-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="ed403-141">Après le rendu du composant</span><span class="sxs-lookup"><span data-stu-id="ed403-141">After component render</span></span>

<span data-ttu-id="ed403-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sont appelées après la fin d’un rendu d’un composant.</span><span class="sxs-lookup"><span data-stu-id="ed403-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="ed403-143">Les références d’élément et de composant sont remplies à ce stade.</span><span class="sxs-lookup"><span data-stu-id="ed403-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="ed403-144">Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, par exemple l’activation de bibliothèques JavaScript tierces qui opèrent sur les éléments DOM rendus.</span><span class="sxs-lookup"><span data-stu-id="ed403-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="ed403-145">Le `firstRender` paramètre pour `OnAfterRenderAsync` et `OnAfterRender` :</span><span class="sxs-lookup"><span data-stu-id="ed403-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="ed403-146">A la valeur `true` la première fois que l’instance de composant est restituée.</span><span class="sxs-lookup"><span data-stu-id="ed403-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="ed403-147">Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="ed403-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="ed403-148">Le travail asynchrone immédiatement après le rendu doit se produire pendant l' `OnAfterRenderAsync` événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="ed403-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="ed403-149">Même si vous retournez un <xref:System.Threading.Tasks.Task> à partir de `OnAfterRenderAsync` , l’infrastructure ne planifie pas un cycle de rendu supplémentaire pour votre composant une fois cette tâche terminée.</span><span class="sxs-lookup"><span data-stu-id="ed403-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="ed403-150">Cela permet d’éviter une boucle de rendu infinie.</span><span class="sxs-lookup"><span data-stu-id="ed403-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="ed403-151">Elle est différente des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois que la tâche retournée est terminée.</span><span class="sxs-lookup"><span data-stu-id="ed403-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="ed403-152">`OnAfterRender`et `OnAfterRenderAsync` *ne sont pas appelés lors du prérendu sur le serveur.*</span><span class="sxs-lookup"><span data-stu-id="ed403-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="ed403-153">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="ed403-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="ed403-154">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="ed403-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="ed403-155">Supprimer l’actualisation de l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="ed403-155">Suppress UI refreshing</span></span>

<span data-ttu-id="ed403-156">Substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour supprimer l’actualisation de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ed403-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="ed403-157">Si l’implémentation retourne `true` , l’interface utilisateur est actualisée :</span><span class="sxs-lookup"><span data-stu-id="ed403-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="ed403-158">`ShouldRender`est appelé chaque fois que le composant est restitué.</span><span class="sxs-lookup"><span data-stu-id="ed403-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="ed403-159">Même si `ShouldRender` est substitué, le composant est toujours restitué initialement.</span><span class="sxs-lookup"><span data-stu-id="ed403-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="ed403-160">Pour plus d'informations, consultez <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="ed403-160">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="ed403-161">Changements d'état</span><span class="sxs-lookup"><span data-stu-id="ed403-161">State changes</span></span>

<span data-ttu-id="ed403-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>notifie le composant que son état a changé.</span><span class="sxs-lookup"><span data-stu-id="ed403-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="ed403-163">Le cas échéant, `StateHasChanged` l’appel de entraîne le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="ed403-163">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="ed403-164">Gérer les actions asynchrones incomplètes au rendu</span><span class="sxs-lookup"><span data-stu-id="ed403-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="ed403-165">Les actions asynchrones exécutées dans des événements de cycle de vie peuvent ne pas être terminées avant le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="ed403-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="ed403-166">Les objets peuvent être `null` ou être remplis de façon incomplète avec des données pendant l’exécution de la méthode Lifecycle.</span><span class="sxs-lookup"><span data-stu-id="ed403-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="ed403-167">Fournissez une logique de rendu pour confirmer que les objets sont initialisés.</span><span class="sxs-lookup"><span data-stu-id="ed403-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="ed403-168">Affichez les éléments d’interface utilisateur d’espace réservé (par exemple, un message de chargement) tandis que les objets sont `null` .</span><span class="sxs-lookup"><span data-stu-id="ed403-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="ed403-169">Dans le `FetchData` composant des Blazor modèles, `OnInitializedAsync` est remplacé par asynchrone recevoir les données de prévision ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="ed403-169">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="ed403-170">Lorsque `forecasts` a `null` la valeur, un message de chargement est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ed403-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="ed403-171">Une fois la `Task` retournée `OnInitializedAsync` terminée, le composant est rerendu avec l’État mis à jour.</span><span class="sxs-lookup"><span data-stu-id="ed403-171">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="ed403-172">*Pages/fetchData. Razor* dans le Blazor modèle de serveur :</span><span class="sxs-lookup"><span data-stu-id="ed403-172">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="ed403-173">Suppression de composant avec IDisposable</span><span class="sxs-lookup"><span data-stu-id="ed403-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="ed403-174">Si un composant implémente <xref:System.IDisposable> , la [méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose) est appelée lorsque le composant est supprimé de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ed403-174">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="ed403-175">Le composant suivant utilise `@implements IDisposable` et la `Dispose` méthode :</span><span class="sxs-lookup"><span data-stu-id="ed403-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="ed403-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>L’appel de dans `Dispose` n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ed403-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="ed403-177">`StateHasChanged`peut être appelé dans le cadre du détachement du convertisseur, donc demander des mises à jour de l’interface utilisateur à ce stade n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ed403-177">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="ed403-178">Annule l’abonnement des gestionnaires d’événements des événements .NET.</span><span class="sxs-lookup"><span data-stu-id="ed403-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="ed403-179">Les exemples de [ Blazor formulaires](xref:blazor/forms-validation) suivants montrent comment décrocher un gestionnaire d’événements dans la `Dispose` méthode :</span><span class="sxs-lookup"><span data-stu-id="ed403-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="ed403-180">Approche de champ privé et lambda</span><span class="sxs-lookup"><span data-stu-id="ed403-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="ed403-181">Approche de la méthode privée</span><span class="sxs-lookup"><span data-stu-id="ed403-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="ed403-182">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="ed403-182">Handle errors</span></span>

<span data-ttu-id="ed403-183">Pour plus d’informations sur la gestion des erreurs lors de l’exécution de la méthode de cycle de vie, consultez <xref:blazor/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="ed403-183">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="ed403-184">Reconnexion avec état après le prérendu</span><span class="sxs-lookup"><span data-stu-id="ed403-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="ed403-185">Dans une Blazor application serveur quand `RenderMode` est `ServerPrerendered` , le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="ed403-185">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="ed403-186">Une fois que le navigateur a établi une connexion au serveur, le composant est *à nouveau*rendu et le composant est maintenant interactif.</span><span class="sxs-lookup"><span data-stu-id="ed403-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="ed403-187">Si la méthode de cycle de vie [OnInitialized {Async}](#component-initialization-methods) pour initialiser le composant est présente, la méthode est exécutée *deux fois*:</span><span class="sxs-lookup"><span data-stu-id="ed403-187">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="ed403-188">Lorsque le composant est prérendu statiquement.</span><span class="sxs-lookup"><span data-stu-id="ed403-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="ed403-189">Après l’établissement de la connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="ed403-189">After the server connection has been established.</span></span>

<span data-ttu-id="ed403-190">Cela peut entraîner une modification notable des données affichées dans l’interface utilisateur lorsque le composant est finalement restitué.</span><span class="sxs-lookup"><span data-stu-id="ed403-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="ed403-191">Pour éviter le double rendu du scénario dans une Blazor application serveur :</span><span class="sxs-lookup"><span data-stu-id="ed403-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="ed403-192">Transmettez un identificateur qui peut être utilisé pour mettre en cache l’État pendant le prérendu et pour récupérer l’état après le redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="ed403-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="ed403-193">Utilisez l’identificateur pendant le prérendu pour enregistrer l’état du composant.</span><span class="sxs-lookup"><span data-stu-id="ed403-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="ed403-194">Utilisez l’identificateur après le prérendu pour récupérer l’État mis en cache.</span><span class="sxs-lookup"><span data-stu-id="ed403-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="ed403-195">Le code suivant illustre une mise à jour `WeatherForecastService` dans une application serveur basée sur un modèle Blazor qui évite le double rendu :</span><span class="sxs-lookup"><span data-stu-id="ed403-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="ed403-196">Pour plus d’informations sur le `RenderMode` , consultez <xref:blazor/hosting-model-configuration#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="ed403-196">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="ed403-197">Détecter quand l’application est prérendu</span><span class="sxs-lookup"><span data-stu-id="ed403-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="ed403-198">Travail en arrière-plan annulable</span><span class="sxs-lookup"><span data-stu-id="ed403-198">Cancelable background work</span></span>

<span data-ttu-id="ed403-199">Les composants effectuent souvent des tâches en arrière-plan de longue durée, telles que la création d’appels réseau ( <xref:System.Net.Http.HttpClient> ) et l’interaction avec les bases de données.</span><span class="sxs-lookup"><span data-stu-id="ed403-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="ed403-200">Il est souhaitable d’arrêter le travail en arrière-plan pour économiser les ressources système dans plusieurs situations.</span><span class="sxs-lookup"><span data-stu-id="ed403-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="ed403-201">Par exemple, les opérations asynchrones en arrière-plan ne s’arrêtent pas automatiquement lorsqu’un utilisateur quitte un composant.</span><span class="sxs-lookup"><span data-stu-id="ed403-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="ed403-202">Les autres raisons pour lesquelles les éléments de travail en arrière-plan peuvent nécessiter l’annulation sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="ed403-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="ed403-203">Une tâche en arrière-plan en cours d’exécution a été démarrée avec des données d’entrée ou des paramètres de traitement défectueux.</span><span class="sxs-lookup"><span data-stu-id="ed403-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="ed403-204">Le jeu actuel d’éléments de travail en arrière-plan doit être remplacé par un nouvel ensemble d’éléments de travail.</span><span class="sxs-lookup"><span data-stu-id="ed403-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="ed403-205">La priorité des tâches en cours d’exécution doit être modifiée.</span><span class="sxs-lookup"><span data-stu-id="ed403-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="ed403-206">L’application doit être arrêtée pour pouvoir être redéployée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ed403-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="ed403-207">Les ressources du serveur sont limitées, ce qui nécessite la replanification des éléments de travail backgound.</span><span class="sxs-lookup"><span data-stu-id="ed403-207">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="ed403-208">Pour implémenter un modèle de travail d’arrière-plan annulable dans un composant :</span><span class="sxs-lookup"><span data-stu-id="ed403-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="ed403-209">Utilisez un <xref:System.Threading.CancellationTokenSource> et un <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="ed403-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="ed403-210">Lors de [la suppression du composant](#component-disposal-with-idisposable) et à tout moment, l’annulation est souhaitée en annulant manuellement le jeton, appelez [CancellationTokenSource. Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) pour signaler que le travail en arrière-plan doit être annulé.</span><span class="sxs-lookup"><span data-stu-id="ed403-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="ed403-211">Une fois l’appel asynchrone retourné, appelez <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> sur le jeton.</span><span class="sxs-lookup"><span data-stu-id="ed403-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="ed403-212">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ed403-212">In the following example:</span></span>

* <span data-ttu-id="ed403-213">`await Task.Delay(5000, cts.Token);`représente le travail en arrière-plan asynchrone de longue durée.</span><span class="sxs-lookup"><span data-stu-id="ed403-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="ed403-214">`BackgroundResourceMethod`représente une méthode d’arrière-plan de longue durée qui ne doit pas démarrer si le `Resource` est supprimé avant l’appel de la méthode.</span><span class="sxs-lookup"><span data-stu-id="ed403-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
