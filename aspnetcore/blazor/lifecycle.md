---
title: cycle de vie Blazor ASP.NET Core
author: guardrex
description: Découvrez comment utiliser les méthodes du cycle de Blazor vie des composants Razor dans ASP.NET applications Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791464"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="7b47f-103">cycle de vie Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b47f-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="7b47f-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7b47f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="7b47f-105">Le Blazor cadre comprend des méthodes de cycle de vie synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="7b47f-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="7b47f-106">Remplacer les méthodes du cycle de vie pour effectuer des opérations supplémentaires sur les composants pendant l’initialisation et le rendu des composants.</span><span class="sxs-lookup"><span data-stu-id="7b47f-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="7b47f-107">Méthodes de cycle de vie</span><span class="sxs-lookup"><span data-stu-id="7b47f-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="7b47f-108">Méthodes d’initialisation des composants</span><span class="sxs-lookup"><span data-stu-id="7b47f-108">Component initialization methods</span></span>

<span data-ttu-id="7b47f-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> sont invoqués lorsque le composant est paradé après avoir reçu ses paramètres initiaux de sa composante mère.</span><span class="sxs-lookup"><span data-stu-id="7b47f-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="7b47f-110">Utilisez `OnInitializedAsync` lorsque le composant effectue une opération asynchrone et doit se rafraîchir lorsque l’opération est terminée.</span><span class="sxs-lookup"><span data-stu-id="7b47f-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="7b47f-111">Pour une opération synchrone, `OnInitialized`remplacez :</span><span class="sxs-lookup"><span data-stu-id="7b47f-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="7b47f-112">Pour effectuer une opération asynchrone, `OnInitializedAsync` remplacer `await` et utiliser le mot clé sur l’opération :</span><span class="sxs-lookup"><span data-stu-id="7b47f-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="7b47f-113">Applications serveur qui [prerender leur appel](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` de contenu **_deux fois_**:</span><span class="sxs-lookup"><span data-stu-id="7b47f-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="7b47f-114">Une fois lorsque le composant est initialement rendu statiquement dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="7b47f-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="7b47f-115">Une deuxième fois lorsque le navigateur établit une connexion vers le serveur.</span><span class="sxs-lookup"><span data-stu-id="7b47f-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="7b47f-116">Pour empêcher le `OnInitializedAsync` code développeur de s’exécuter deux fois, voir la [reconnexion Stateful après la section de prérendering.](#stateful-reconnection-after-prerendering)</span><span class="sxs-lookup"><span data-stu-id="7b47f-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="7b47f-117">Alors Blazor qu’une application Server est pré-implantation, certaines actions, telles que l’appel dans JavaScript, ne sont pas possibles parce qu’une connexion avec le navigateur n’a pas été établie.</span><span class="sxs-lookup"><span data-stu-id="7b47f-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="7b47f-118">Les composants peuvent devoir rendre différemment lorsqu’ils sont préditeurs.</span><span class="sxs-lookup"><span data-stu-id="7b47f-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="7b47f-119">Pour plus d’informations, voir le [Détecter lorsque l’application est la section de pré-endering.](#detect-when-the-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="7b47f-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="7b47f-120">Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination.</span><span class="sxs-lookup"><span data-stu-id="7b47f-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7b47f-121">Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="7b47f-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="7b47f-122">Avant que les paramètres ne soient définis</span><span class="sxs-lookup"><span data-stu-id="7b47f-122">Before parameters are set</span></span>

<span data-ttu-id="7b47f-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>définit les paramètres fournis par le parent du composant dans l’arbre de rendu :</span><span class="sxs-lookup"><span data-stu-id="7b47f-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="7b47f-124"><xref:Microsoft.AspNetCore.Components.ParameterView>contient l’ensemble des valeurs `SetParametersAsync` de paramètres chaque fois qu’on appelle.</span><span class="sxs-lookup"><span data-stu-id="7b47f-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="7b47f-125">La mise `SetParametersAsync` en œuvre par `[Parameter]` défaut `[CascadingParameter]` de définit la valeur `ParameterView`de chaque propriété avec l’attribut ou qui a une valeur correspondante dans le .</span><span class="sxs-lookup"><span data-stu-id="7b47f-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="7b47f-126">Les paramètres qui n’ont pas `ParameterView` de valeur correspondante sont laissés inchangés.</span><span class="sxs-lookup"><span data-stu-id="7b47f-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="7b47f-127">Si `base.SetParametersAync` elle n’est pas invoquée, le code personnalisé peut interpréter la valeur des paramètres entrants de quelque manière que ce soit.</span><span class="sxs-lookup"><span data-stu-id="7b47f-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="7b47f-128">Par exemple, il n’est pas nécessaire d’attribuer les paramètres entrants aux propriétés de la classe.</span><span class="sxs-lookup"><span data-stu-id="7b47f-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="7b47f-129">Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination.</span><span class="sxs-lookup"><span data-stu-id="7b47f-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7b47f-130">Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="7b47f-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="7b47f-131">Après l’ensemble des paramètres</span><span class="sxs-lookup"><span data-stu-id="7b47f-131">After parameters are set</span></span>

<span data-ttu-id="7b47f-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> sont appelés:</span><span class="sxs-lookup"><span data-stu-id="7b47f-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="7b47f-133">Lorsque le composant est parasécé et a reçu sa première série de paramètres de sa composante mère.</span><span class="sxs-lookup"><span data-stu-id="7b47f-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="7b47f-134">Lorsque la composante parente restitue et fournit :</span><span class="sxs-lookup"><span data-stu-id="7b47f-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="7b47f-135">Seuls les types immuables primitifs connus dont au moins un paramètre a changé.</span><span class="sxs-lookup"><span data-stu-id="7b47f-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="7b47f-136">Tous les paramètres de type complexe.</span><span class="sxs-lookup"><span data-stu-id="7b47f-136">Any complex-typed parameters.</span></span> <span data-ttu-id="7b47f-137">Le cadre ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, de sorte qu’il traite le paramètre défini comme modifié.</span><span class="sxs-lookup"><span data-stu-id="7b47f-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="7b47f-138">Le travail asynchrone lors de l’application `OnParametersSetAsync` des paramètres et des valeurs de propriété doit se produire pendant l’événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="7b47f-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="7b47f-139">Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination.</span><span class="sxs-lookup"><span data-stu-id="7b47f-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7b47f-140">Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="7b47f-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="7b47f-141">Après le rendu du composant</span><span class="sxs-lookup"><span data-stu-id="7b47f-141">After component render</span></span>

<span data-ttu-id="7b47f-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> sont appelés après qu’un composant a terminé le rendu.</span><span class="sxs-lookup"><span data-stu-id="7b47f-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="7b47f-143">Les références d’éléments et de composants sont peuplées à ce stade.</span><span class="sxs-lookup"><span data-stu-id="7b47f-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="7b47f-144">Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, comme l’activation de bibliothèques JavaScript tierces qui fonctionnent sur les éléments DOM rendus.</span><span class="sxs-lookup"><span data-stu-id="7b47f-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="7b47f-145">Le `firstRender` paramètre pour `OnAfterRenderAsync` et `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="7b47f-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="7b47f-146">Est réglé `true` à la première fois que l’instance de composant est rendue.</span><span class="sxs-lookup"><span data-stu-id="7b47f-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="7b47f-147">Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="7b47f-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="7b47f-148">Le travail asynchrone immédiatement après le `OnAfterRenderAsync` rendu doit se produire pendant l’événement de cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="7b47f-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="7b47f-149">Même si vous <xref:System.Threading.Tasks.Task> `OnAfterRenderAsync`retournez un de , le cadre ne prévoit pas un cycle de rendu supplémentaire pour votre composant une fois que cette tâche se termine.</span><span class="sxs-lookup"><span data-stu-id="7b47f-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="7b47f-150">Il s’agit d’éviter une boucle de rendu infinie.</span><span class="sxs-lookup"><span data-stu-id="7b47f-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="7b47f-151">Il est différent des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois la tâche retournée terminée.</span><span class="sxs-lookup"><span data-stu-id="7b47f-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="7b47f-152">`OnAfterRender`et `OnAfterRenderAsync` *ne sont pas appelés lors du prerendering sur le serveur.*</span><span class="sxs-lookup"><span data-stu-id="7b47f-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="7b47f-153">Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination.</span><span class="sxs-lookup"><span data-stu-id="7b47f-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7b47f-154">Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="7b47f-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="7b47f-155">Supprimer l’interface utilisateur rafraîchissante</span><span class="sxs-lookup"><span data-stu-id="7b47f-155">Suppress UI refreshing</span></span>

<span data-ttu-id="7b47f-156">Remplacement <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pour supprimer l’interface utilisateur rafraîchissante.</span><span class="sxs-lookup"><span data-stu-id="7b47f-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="7b47f-157">Si la `true`mise en œuvre revient, l’interface utilisateur est actualisée :</span><span class="sxs-lookup"><span data-stu-id="7b47f-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="7b47f-158">`ShouldRender`est appelé chaque fois que le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="7b47f-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="7b47f-159">Même `ShouldRender` s’il est remplacé, le composant est toujours rendu au départ.</span><span class="sxs-lookup"><span data-stu-id="7b47f-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="7b47f-160">Changements d'état</span><span class="sxs-lookup"><span data-stu-id="7b47f-160">State changes</span></span>

<span data-ttu-id="7b47f-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>informe la composante que son état a changé.</span><span class="sxs-lookup"><span data-stu-id="7b47f-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="7b47f-162">Le cas `StateHasChanged` échéant, l’appel provoque la remise du composant.</span><span class="sxs-lookup"><span data-stu-id="7b47f-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="7b47f-163">Gérer les actions async incomplètes au rendu</span><span class="sxs-lookup"><span data-stu-id="7b47f-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="7b47f-164">Les actions asynchrones effectuées dans les événements du cycle de vie n’ont peut-être pas été accomplies avant que le composant ne soit rendu.</span><span class="sxs-lookup"><span data-stu-id="7b47f-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="7b47f-165">Les objets `null` peuvent être ou incomplètement peuplés de données pendant l’exécution de la méthode du cycle de vie.</span><span class="sxs-lookup"><span data-stu-id="7b47f-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="7b47f-166">Fournir une logique de rendu pour confirmer que les objets sont initialisés.</span><span class="sxs-lookup"><span data-stu-id="7b47f-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="7b47f-167">Rendre les éléments d’interface utilisateur titulaires de `null`place (par exemple, un message de chargement) tandis que les objets sont .</span><span class="sxs-lookup"><span data-stu-id="7b47f-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="7b47f-168">Dans `FetchData` la composante Blazor des `OnInitializedAsync` modèles, est remplacé à asychronously`forecasts`recevoir des données de prévision ( ).</span><span class="sxs-lookup"><span data-stu-id="7b47f-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="7b47f-169">Quand `forecasts` `null`est, un message de chargement est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b47f-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="7b47f-170">Après `Task` le `OnInitializedAsync` retour par complète, le composant est rendu avec l’état mis à jour.</span><span class="sxs-lookup"><span data-stu-id="7b47f-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="7b47f-171">*Pages/FetchData.razor* dans Blazor le modèle Server :</span><span class="sxs-lookup"><span data-stu-id="7b47f-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="7b47f-172">Élimination des composants avec IDisposable</span><span class="sxs-lookup"><span data-stu-id="7b47f-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="7b47f-173">Si un composant <xref:System.IDisposable>s’implémente, la [méthode d’élimination](/dotnet/standard/garbage-collection/implementing-dispose) est appelée lorsque le composant est retiré de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b47f-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="7b47f-174">Le composant `@implements IDisposable` suivant `Dispose` utilise et la méthode :</span><span class="sxs-lookup"><span data-stu-id="7b47f-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="7b47f-175">Appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7b47f-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="7b47f-176">`StateHasChanged`pourrait être invoqué dans le cadre de la démolition du rendu, de sorte que demander des mises à jour de l’interface utilisateur à ce moment-là n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="7b47f-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="7b47f-177">Désabonner les gestionnaires d’événements des événements .NET.</span><span class="sxs-lookup"><span data-stu-id="7b47f-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="7b47f-178">Les exemples [ Blazor de formulaires](xref:blazor/forms-validation) suivants montrent comment `Dispose` débrancher un gestionnaire d’événements dans la méthode :</span><span class="sxs-lookup"><span data-stu-id="7b47f-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="7b47f-179">Champ privé et approche lambda</span><span class="sxs-lookup"><span data-stu-id="7b47f-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="7b47f-180">Approche de méthode privée</span><span class="sxs-lookup"><span data-stu-id="7b47f-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="7b47f-181">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="7b47f-181">Handle errors</span></span>

<span data-ttu-id="7b47f-182">Pour plus d’informations sur le <xref:blazor/handle-errors#lifecycle-methods>traitement des erreurs pendant l’exécution de la méthode du cycle de vie, voir .</span><span class="sxs-lookup"><span data-stu-id="7b47f-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="7b47f-183">Reconnexion stateful après prerendering</span><span class="sxs-lookup"><span data-stu-id="7b47f-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="7b47f-184">Dans Blazor une application `RenderMode` `ServerPrerendered`Server quand il est, le composant est initialement rendu statiquement dans le cadre de la page.</span><span class="sxs-lookup"><span data-stu-id="7b47f-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="7b47f-185">Une fois que le navigateur établit une connexion vers le serveur, le composant est rendu *à nouveau,* et le composant est maintenant interactif.</span><span class="sxs-lookup"><span data-stu-id="7b47f-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="7b47f-186">Si la méthode du cycle de vie [OnInitialized-AsyncMD](#component-initialization-methods) pour l’initialisation du composant est présente, la méthode est exécutée *deux fois*:</span><span class="sxs-lookup"><span data-stu-id="7b47f-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="7b47f-187">Lorsque le composant est prédité statiquement.</span><span class="sxs-lookup"><span data-stu-id="7b47f-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="7b47f-188">Après la connexion du serveur a été établi.</span><span class="sxs-lookup"><span data-stu-id="7b47f-188">After the server connection has been established.</span></span>

<span data-ttu-id="7b47f-189">Cela peut entraîner un changement notable dans les données affichées dans l’interface utilisateur lorsque le composant est finalement rendu.</span><span class="sxs-lookup"><span data-stu-id="7b47f-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="7b47f-190">Pour éviter le scénario de Blazor double rendu dans une application Server :</span><span class="sxs-lookup"><span data-stu-id="7b47f-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="7b47f-191">Passez dans un identifiant qui peut être utilisé pour mettre en cache l’état pendant le pré-prérender et pour récupérer l’état après le redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b47f-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="7b47f-192">Utilisez l’identifiant pendant le prérendering pour enregistrer l’état du composant.</span><span class="sxs-lookup"><span data-stu-id="7b47f-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="7b47f-193">Utilisez l’identifiant après le prérender pour récupérer l’état mis en cache.</span><span class="sxs-lookup"><span data-stu-id="7b47f-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="7b47f-194">Le code suivant montre `WeatherForecastService` une mise Blazor à jour d’une application Server basée sur un modèle qui évite le double rendu :</span><span class="sxs-lookup"><span data-stu-id="7b47f-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="7b47f-195">Pour plus d’informations sur le `RenderMode`, voir <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="7b47f-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="7b47f-196">Détecter quand l’application est en pré-endering</span><span class="sxs-lookup"><span data-stu-id="7b47f-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
