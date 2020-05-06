---
title: Cycle Blazor de vie ASP.net Core
author: guardrex
description: Découvrez comment utiliser Razor les méthodes de cycle de vie Blazor des composants dans les applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 87c65776684f9cc91b868b8e88926e46b25592ff
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771518"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="171cc-103">Cycle Blazor de vie ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="171cc-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="171cc-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="171cc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="171cc-105">L' Blazor infrastructure comprend des méthodes de cycle de vie synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="171cc-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="171cc-106">Substituez les méthodes de cycle de vie pour effectuer des opérations supplémentaires sur les composants lors de l’initialisation et du rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="171cc-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="171cc-107">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="171cc-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="171cc-108">Méthodes d’initialisation de composant</span><span class="sxs-lookup"><span data-stu-id="171cc-108">Component initialization methods</span></span>

<span data-ttu-id="171cc-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> sont appelés lorsque le composant est initialisé après avoir reçu ses paramètres initiaux de son composant parent.</span><span class="sxs-lookup"><span data-stu-id="171cc-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="171cc-110">Utilisez `OnInitializedAsync` lorsque le composant exécute une opération asynchrone et doit être actualisé lorsque l’opération est terminée.</span><span class="sxs-lookup"><span data-stu-id="171cc-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="171cc-111">Pour une opération synchrone, remplacez `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="171cc-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="171cc-112">Pour effectuer une opération asynchrone, substituez `OnInitializedAsync` et utilisez le `await` mot clé sur l’opération :</span><span class="sxs-lookup"><span data-stu-id="171cc-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="171cc-113">Applications serveur qui [préaffichent le contenu](xref:blazor/hosting-model-configuration#render-mode) de l’appel `OnInitializedAsync` à **_deux reprises_**:</span><span class="sxs-lookup"><span data-stu-id="171cc-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="171cc-114">Une fois lorsque le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="171cc-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="171cc-115">Une deuxième fois lorsque le navigateur établit une connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="171cc-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="171cc-116">Pour empêcher l’exécution à `OnInitializedAsync` deux reprises du code du développeur, consultez la section [reconnexion avec état après le prérendu](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="171cc-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="171cc-117">Pendant le Blazor prérendu d’une application serveur, certaines actions, telles que l’appel de JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie.</span><span class="sxs-lookup"><span data-stu-id="171cc-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="171cc-118">Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus.</span><span class="sxs-lookup"><span data-stu-id="171cc-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="171cc-119">Pour plus d’informations, consultez la section [détecter quand l’application est un prérendu](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="171cc-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="171cc-120">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="171cc-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="171cc-121">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="171cc-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="171cc-122">Les paramètres Before sont définis</span><span class="sxs-lookup"><span data-stu-id="171cc-122">Before parameters are set</span></span>

<span data-ttu-id="171cc-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>définit les paramètres fournis par le parent du composant dans l’arborescence de rendu :</span><span class="sxs-lookup"><span data-stu-id="171cc-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="171cc-124"><xref:Microsoft.AspNetCore.Components.ParameterView>contient l’ensemble des valeurs de paramètre à chaque `SetParametersAsync` fois que est appelé.</span><span class="sxs-lookup"><span data-stu-id="171cc-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="171cc-125">L’implémentation par défaut `SetParametersAsync` de définit la valeur de chaque propriété avec `[Parameter]` l' `[CascadingParameter]` attribut ou ayant une valeur correspondante dans le `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="171cc-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="171cc-126">Les paramètres qui n’ont pas de valeur `ParameterView` correspondante dans ne sont pas modifiés.</span><span class="sxs-lookup"><span data-stu-id="171cc-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="171cc-127">Si `base.SetParametersAync` n’est pas appelé, le code personnalisé peut interpréter la valeur des paramètres entrants de la façon requise.</span><span class="sxs-lookup"><span data-stu-id="171cc-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="171cc-128">Par exemple, il n’est pas nécessaire d’assigner les paramètres entrants aux propriétés de la classe.</span><span class="sxs-lookup"><span data-stu-id="171cc-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="171cc-129">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="171cc-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="171cc-130">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="171cc-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="171cc-131">Une fois les paramètres définis</span><span class="sxs-lookup"><span data-stu-id="171cc-131">After parameters are set</span></span>

<span data-ttu-id="171cc-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> sont appelées :</span><span class="sxs-lookup"><span data-stu-id="171cc-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="171cc-133">Lorsque le composant est initialisé et a reçu son premier jeu de paramètres de son composant parent.</span><span class="sxs-lookup"><span data-stu-id="171cc-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="171cc-134">Lorsque le composant parent est à nouveau rendu et fournit :</span><span class="sxs-lookup"><span data-stu-id="171cc-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="171cc-135">Seuls les types immuables primitifs connus dont au moins un paramètre a été modifié.</span><span class="sxs-lookup"><span data-stu-id="171cc-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="171cc-136">Tous les paramètres de type complexe.</span><span class="sxs-lookup"><span data-stu-id="171cc-136">Any complex-typed parameters.</span></span> <span data-ttu-id="171cc-137">L’infrastructure ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, donc elle traite le jeu de paramètres comme étant modifié.</span><span class="sxs-lookup"><span data-stu-id="171cc-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="171cc-138">Un travail asynchrone lors de l’application de paramètres et de valeurs `OnParametersSetAsync` de propriété doit se produire pendant l’événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="171cc-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="171cc-139">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="171cc-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="171cc-140">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="171cc-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="171cc-141">Après le rendu du composant</span><span class="sxs-lookup"><span data-stu-id="171cc-141">After component render</span></span>

<span data-ttu-id="171cc-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> sont appelées après la fin d’un rendu d’un composant.</span><span class="sxs-lookup"><span data-stu-id="171cc-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="171cc-143">Les références d’élément et de composant sont remplies à ce stade.</span><span class="sxs-lookup"><span data-stu-id="171cc-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="171cc-144">Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, par exemple l’activation de bibliothèques JavaScript tierces qui opèrent sur les éléments DOM rendus.</span><span class="sxs-lookup"><span data-stu-id="171cc-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="171cc-145">Le `firstRender` paramètre pour `OnAfterRenderAsync` et `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="171cc-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="171cc-146">A `true` la valeur la première fois que l’instance de composant est restituée.</span><span class="sxs-lookup"><span data-stu-id="171cc-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="171cc-147">Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="171cc-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="171cc-148">Le travail asynchrone immédiatement après le rendu doit se `OnAfterRenderAsync` produire pendant l’événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="171cc-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="171cc-149">Même si vous retournez un <xref:System.Threading.Tasks.Task> à `OnAfterRenderAsync`partir de, l’infrastructure ne planifie pas un cycle de rendu supplémentaire pour votre composant une fois cette tâche terminée.</span><span class="sxs-lookup"><span data-stu-id="171cc-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="171cc-150">Cela permet d’éviter une boucle de rendu infinie.</span><span class="sxs-lookup"><span data-stu-id="171cc-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="171cc-151">Elle est différente des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois que la tâche retournée est terminée.</span><span class="sxs-lookup"><span data-stu-id="171cc-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="171cc-152">`OnAfterRender`et `OnAfterRenderAsync` *ne sont pas appelés lors du prérendu sur le serveur.*</span><span class="sxs-lookup"><span data-stu-id="171cc-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="171cc-153">Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression.</span><span class="sxs-lookup"><span data-stu-id="171cc-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="171cc-154">Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="171cc-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="171cc-155">Supprimer l’actualisation de l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="171cc-155">Suppress UI refreshing</span></span>

<span data-ttu-id="171cc-156">Substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pour supprimer l’actualisation de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="171cc-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="171cc-157">Si l’implémentation retourne `true`, l’interface utilisateur est actualisée :</span><span class="sxs-lookup"><span data-stu-id="171cc-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="171cc-158">`ShouldRender`est appelé chaque fois que le composant est restitué.</span><span class="sxs-lookup"><span data-stu-id="171cc-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="171cc-159">Même si `ShouldRender` est substitué, le composant est toujours restitué initialement.</span><span class="sxs-lookup"><span data-stu-id="171cc-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="171cc-160">Changements d'état</span><span class="sxs-lookup"><span data-stu-id="171cc-160">State changes</span></span>

<span data-ttu-id="171cc-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>notifie le composant que son état a changé.</span><span class="sxs-lookup"><span data-stu-id="171cc-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="171cc-162">Le cas échéant, `StateHasChanged` l’appel de entraîne le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="171cc-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="171cc-163">Gérer les actions asynchrones incomplètes au rendu</span><span class="sxs-lookup"><span data-stu-id="171cc-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="171cc-164">Les actions asynchrones exécutées dans des événements de cycle de vie peuvent ne pas être terminées avant le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="171cc-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="171cc-165">Les objets peuvent `null` être ou être remplis de façon incomplète avec des données pendant l’exécution de la méthode Lifecycle.</span><span class="sxs-lookup"><span data-stu-id="171cc-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="171cc-166">Fournissez une logique de rendu pour confirmer que les objets sont initialisés.</span><span class="sxs-lookup"><span data-stu-id="171cc-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="171cc-167">Affichez les éléments d’interface utilisateur d’espace réservé (par exemple, un `null`message de chargement) tandis que les objets sont.</span><span class="sxs-lookup"><span data-stu-id="171cc-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="171cc-168">Dans le `FetchData` composant des Blazor modèles, `OnInitializedAsync` est remplacé par asynchrone recevoir les données de prévision (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="171cc-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="171cc-169">Lorsque `forecasts` a `null`la valeur, un message de chargement est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="171cc-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="171cc-170">Une fois `Task` la retournée `OnInitializedAsync` terminée, le composant est rerendu avec l’État mis à jour.</span><span class="sxs-lookup"><span data-stu-id="171cc-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="171cc-171">*Pages/fetchData. Razor* dans le Blazor modèle de serveur :</span><span class="sxs-lookup"><span data-stu-id="171cc-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="171cc-172">Suppression de composant avec IDisposable</span><span class="sxs-lookup"><span data-stu-id="171cc-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="171cc-173">Si un composant implémente <xref:System.IDisposable>, la [méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose) est appelée lorsque le composant est supprimé de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="171cc-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="171cc-174">Le composant suivant utilise `@implements IDisposable` et la `Dispose` méthode :</span><span class="sxs-lookup"><span data-stu-id="171cc-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="171cc-175">L' <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> appel `Dispose` de dans n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="171cc-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="171cc-176">`StateHasChanged`peut être appelé dans le cadre du détachement du convertisseur, donc demander des mises à jour de l’interface utilisateur à ce stade n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="171cc-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="171cc-177">Annule l’abonnement des gestionnaires d’événements des événements .NET.</span><span class="sxs-lookup"><span data-stu-id="171cc-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="171cc-178">Les exemples de [ Blazor formulaires](xref:blazor/forms-validation) suivants montrent comment décrocher un gestionnaire d’événements dans `Dispose` la méthode :</span><span class="sxs-lookup"><span data-stu-id="171cc-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="171cc-179">Approche de champ privé et lambda</span><span class="sxs-lookup"><span data-stu-id="171cc-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="171cc-180">Approche de la méthode privée</span><span class="sxs-lookup"><span data-stu-id="171cc-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="171cc-181">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="171cc-181">Handle errors</span></span>

<span data-ttu-id="171cc-182">Pour plus d’informations sur la gestion des erreurs lors de <xref:blazor/handle-errors#lifecycle-methods>l’exécution de la méthode de cycle de vie, consultez.</span><span class="sxs-lookup"><span data-stu-id="171cc-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="171cc-183">Reconnexion avec état après le prérendu</span><span class="sxs-lookup"><span data-stu-id="171cc-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="171cc-184">Dans une Blazor application serveur quand `RenderMode` est `ServerPrerendered`, le composant est initialement restitué de manière statique dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="171cc-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="171cc-185">Une fois que le navigateur a établi une connexion au serveur, le composant est *à nouveau*rendu et le composant est maintenant interactif.</span><span class="sxs-lookup"><span data-stu-id="171cc-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="171cc-186">Si la méthode de cycle de vie [OnInitialized {Async}](#component-initialization-methods) pour initialiser le composant est présente, la méthode est exécutée *deux fois*:</span><span class="sxs-lookup"><span data-stu-id="171cc-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="171cc-187">Lorsque le composant est prérendu statiquement.</span><span class="sxs-lookup"><span data-stu-id="171cc-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="171cc-188">Après l’établissement de la connexion au serveur.</span><span class="sxs-lookup"><span data-stu-id="171cc-188">After the server connection has been established.</span></span>

<span data-ttu-id="171cc-189">Cela peut entraîner une modification notable des données affichées dans l’interface utilisateur lorsque le composant est finalement restitué.</span><span class="sxs-lookup"><span data-stu-id="171cc-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="171cc-190">Pour éviter le double rendu du scénario dans une Blazor application serveur :</span><span class="sxs-lookup"><span data-stu-id="171cc-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="171cc-191">Transmettez un identificateur qui peut être utilisé pour mettre en cache l’État pendant le prérendu et pour récupérer l’état après le redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="171cc-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="171cc-192">Utilisez l’identificateur pendant le prérendu pour enregistrer l’état du composant.</span><span class="sxs-lookup"><span data-stu-id="171cc-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="171cc-193">Utilisez l’identificateur après le prérendu pour récupérer l’État mis en cache.</span><span class="sxs-lookup"><span data-stu-id="171cc-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="171cc-194">Le code suivant illustre une mise `WeatherForecastService` à jour dans une application Blazor serveur basée sur un modèle qui évite le double rendu :</span><span class="sxs-lookup"><span data-stu-id="171cc-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="171cc-195">Pour plus d’informations sur `RenderMode`le, <xref:blazor/hosting-model-configuration#render-mode>consultez.</span><span class="sxs-lookup"><span data-stu-id="171cc-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="171cc-196">Détecter quand l’application est prérendu</span><span class="sxs-lookup"><span data-stu-id="171cc-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
