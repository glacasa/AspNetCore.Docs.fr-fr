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
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>cycle de vie Blazor ASP.NET Core

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Le Blazor cadre comprend des méthodes de cycle de vie synchrones et asynchrones. Remplacer les méthodes du cycle de vie pour effectuer des opérations supplémentaires sur les composants pendant l’initialisation et le rendu des composants.

## <a name="lifecycle-methods"></a>Méthodes de cycle de vie

### <a name="component-initialization-methods"></a>Méthodes d’initialisation des composants

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> sont invoqués lorsque le composant est paradé après avoir reçu ses paramètres initiaux de sa composante mère. Utilisez `OnInitializedAsync` lorsque le composant effectue une opération asynchrone et doit se rafraîchir lorsque l’opération est terminée.

Pour une opération synchrone, `OnInitialized`remplacez :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Pour effectuer une opération asynchrone, `OnInitializedAsync` remplacer `await` et utiliser le mot clé sur l’opération :

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorApplications serveur qui [prerender leur appel](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` de contenu **_deux fois_**:

* Une fois lorsque le composant est initialement rendu statiquement dans le cadre de la page.
* Une deuxième fois lorsque le navigateur établit une connexion vers le serveur.

Pour empêcher le `OnInitializedAsync` code développeur de s’exécuter deux fois, voir la [reconnexion Stateful après la section de prérendering.](#stateful-reconnection-after-prerendering)

Alors Blazor qu’une application Server est pré-implantation, certaines actions, telles que l’appel dans JavaScript, ne sont pas possibles parce qu’une connexion avec le navigateur n’a pas été établie. Les composants peuvent devoir rendre différemment lorsqu’ils sont préditeurs. Pour plus d’informations, voir le [Détecter lorsque l’application est la section de pré-endering.](#detect-when-the-app-is-prerendering)

Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination. Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)

### <a name="before-parameters-are-set"></a>Avant que les paramètres ne soient définis

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>définit les paramètres fournis par le parent du composant dans l’arbre de rendu :

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>contient l’ensemble des valeurs `SetParametersAsync` de paramètres chaque fois qu’on appelle.

La mise `SetParametersAsync` en œuvre par `[Parameter]` défaut `[CascadingParameter]` de définit la valeur `ParameterView`de chaque propriété avec l’attribut ou qui a une valeur correspondante dans le . Les paramètres qui n’ont pas `ParameterView` de valeur correspondante sont laissés inchangés.

Si `base.SetParametersAync` elle n’est pas invoquée, le code personnalisé peut interpréter la valeur des paramètres entrants de quelque manière que ce soit. Par exemple, il n’est pas nécessaire d’attribuer les paramètres entrants aux propriétés de la classe.

Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination. Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)

### <a name="after-parameters-are-set"></a>Après l’ensemble des paramètres

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> sont appelés:

* Lorsque le composant est parasécé et a reçu sa première série de paramètres de sa composante mère.
* Lorsque la composante parente restitue et fournit :
  * Seuls les types immuables primitifs connus dont au moins un paramètre a changé.
  * Tous les paramètres de type complexe. Le cadre ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, de sorte qu’il traite le paramètre défini comme modifié.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Le travail asynchrone lors de l’application `OnParametersSetAsync` des paramètres et des valeurs de propriété doit se produire pendant l’événement de cycle de vie.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination. Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)

### <a name="after-component-render"></a>Après le rendu du composant

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> sont appelés après qu’un composant a terminé le rendu. Les références d’éléments et de composants sont peuplées à ce stade. Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, comme l’activation de bibliothèques JavaScript tierces qui fonctionnent sur les éléments DOM rendus.

Le `firstRender` paramètre pour `OnAfterRenderAsync` et `OnAfterRender`:

* Est réglé `true` à la première fois que l’instance de composant est rendue.
* Peut être utilisé pour s’assurer que le travail d’initialisation n’est effectué qu’une seule fois.

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
> Le travail asynchrone immédiatement après le `OnAfterRenderAsync` rendu doit se produire pendant l’événement de cycle de vie.
>
> Même si vous <xref:System.Threading.Tasks.Task> `OnAfterRenderAsync`retournez un de , le cadre ne prévoit pas un cycle de rendu supplémentaire pour votre composant une fois que cette tâche se termine. Il s’agit d’éviter une boucle de rendu infinie. Il est différent des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois la tâche retournée terminée.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`et `OnAfterRenderAsync` *ne sont pas appelés lors du prerendering sur le serveur.*

Si des gestionnaires d’événements sont mis en place, débaisez-les sur l’élimination. Pour plus d’informations, voir [l’élimination des composants avec la section IDisposable.](#component-disposal-with-idisposable)

### <a name="suppress-ui-refreshing"></a>Supprimer l’interface utilisateur rafraîchissante

Remplacement <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pour supprimer l’interface utilisateur rafraîchissante. Si la `true`mise en œuvre revient, l’interface utilisateur est actualisée :

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`est appelé chaque fois que le composant est rendu.

Même `ShouldRender` s’il est remplacé, le composant est toujours rendu au départ.

## <a name="state-changes"></a>Changements d'état

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>informe la composante que son état a changé. Le cas `StateHasChanged` échéant, l’appel provoque la remise du composant.

## <a name="handle-incomplete-async-actions-at-render"></a>Gérer les actions async incomplètes au rendu

Les actions asynchrones effectuées dans les événements du cycle de vie n’ont peut-être pas été accomplies avant que le composant ne soit rendu. Les objets `null` peuvent être ou incomplètement peuplés de données pendant l’exécution de la méthode du cycle de vie. Fournir une logique de rendu pour confirmer que les objets sont initialisés. Rendre les éléments d’interface utilisateur titulaires de `null`place (par exemple, un message de chargement) tandis que les objets sont .

Dans `FetchData` la composante Blazor des `OnInitializedAsync` modèles, est remplacé à asychronously`forecasts`recevoir des données de prévision ( ). Quand `forecasts` `null`est, un message de chargement est affiché à l’utilisateur. Après `Task` le `OnInitializedAsync` retour par complète, le composant est rendu avec l’état mis à jour.

*Pages/FetchData.razor* dans Blazor le modèle Server :

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Élimination des composants avec IDisposable

Si un composant <xref:System.IDisposable>s’implémente, la [méthode d’élimination](/dotnet/standard/garbage-collection/implementing-dispose) est appelée lorsque le composant est retiré de l’interface utilisateur. Le composant `@implements IDisposable` suivant `Dispose` utilise et la méthode :

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
> Appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` n’est pas pris en charge. `StateHasChanged`pourrait être invoqué dans le cadre de la démolition du rendu, de sorte que demander des mises à jour de l’interface utilisateur à ce moment-là n’est pas pris en charge.

Désabonner les gestionnaires d’événements des événements .NET. Les exemples [ Blazor de formulaires](xref:blazor/forms-validation) suivants montrent comment `Dispose` débrancher un gestionnaire d’événements dans la méthode :

* Champ privé et approche lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Approche de méthode privée

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Gérer les erreurs

Pour plus d’informations sur le <xref:blazor/handle-errors#lifecycle-methods>traitement des erreurs pendant l’exécution de la méthode du cycle de vie, voir .

## <a name="stateful-reconnection-after-prerendering"></a>Reconnexion stateful après prerendering

Dans Blazor une application `RenderMode` `ServerPrerendered`Server quand il est, le composant est initialement rendu statiquement dans le cadre de la page. Une fois que le navigateur établit une connexion vers le serveur, le composant est rendu *à nouveau,* et le composant est maintenant interactif. Si la méthode du cycle de vie [OnInitialized-AsyncMD](#component-initialization-methods) pour l’initialisation du composant est présente, la méthode est exécutée *deux fois*:

* Lorsque le composant est prédité statiquement.
* Après la connexion du serveur a été établi.

Cela peut entraîner un changement notable dans les données affichées dans l’interface utilisateur lorsque le composant est finalement rendu.

Pour éviter le scénario de Blazor double rendu dans une application Server :

* Passez dans un identifiant qui peut être utilisé pour mettre en cache l’état pendant le pré-prérender et pour récupérer l’état après le redémarrage de l’application.
* Utilisez l’identifiant pendant le prérendering pour enregistrer l’état du composant.
* Utilisez l’identifiant après le prérender pour récupérer l’état mis en cache.

Le code suivant montre `WeatherForecastService` une mise Blazor à jour d’une application Server basée sur un modèle qui évite le double rendu :

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

Pour plus d’informations sur le `RenderMode`, voir <xref:blazor/hosting-model-configuration#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Détecter quand l’application est en pré-endering

[!INCLUDE[](~/includes/blazor-prerendering.md)]
