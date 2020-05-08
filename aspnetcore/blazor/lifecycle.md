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
ms.openlocfilehash: 571f14247efe08ac6abbd6d1e2720656f94c213c
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967452"
---
# <a name="aspnet-core-blazor-lifecycle"></a>Cycle Blazor de vie ASP.net Core

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

L' Blazor infrastructure comprend des méthodes de cycle de vie synchrones et asynchrones. Substituez les méthodes de cycle de vie pour effectuer des opérations supplémentaires sur les composants lors de l’initialisation et du rendu des composants.

## <a name="lifecycle-methods"></a>Méthodes de cycle de vie

### <a name="component-initialization-methods"></a>Méthodes d’initialisation de composant

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> sont appelés lorsque le composant est initialisé après avoir reçu ses paramètres initiaux de son composant parent. Utilisez `OnInitializedAsync` lorsque le composant exécute une opération asynchrone et doit être actualisé lorsque l’opération est terminée.

Pour une opération synchrone, remplacez `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Pour effectuer une opération asynchrone, substituez `OnInitializedAsync` et utilisez le `await` mot clé sur l’opération :

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorApplications serveur qui [préaffichent le contenu](xref:blazor/hosting-model-configuration#render-mode) de l’appel `OnInitializedAsync` à **_deux reprises_**:

* Une fois lorsque le composant est initialement restitué de manière statique dans le cadre de la page.
* Une deuxième fois lorsque le navigateur établit une connexion au serveur.

Pour empêcher l’exécution à `OnInitializedAsync` deux reprises du code du développeur, consultez la section [reconnexion avec état après le prérendu](#stateful-reconnection-after-prerendering) .

Pendant le Blazor prérendu d’une application serveur, certaines actions, telles que l’appel de JavaScript, ne sont pas possibles, car une connexion avec le navigateur n’a pas été établie. Les composants peuvent avoir besoin d’être restitués différemment lorsqu’ils sont prérendus. Pour plus d’informations, consultez la section [détecter quand l’application est un prérendu](#detect-when-the-app-is-prerendering) .

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .

### <a name="before-parameters-are-set"></a>Les paramètres Before sont définis

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>définit les paramètres fournis par le parent du composant dans l’arborescence de rendu :

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>contient l’ensemble des valeurs de paramètre à chaque `SetParametersAsync` fois que est appelé.

L’implémentation par défaut `SetParametersAsync` de définit la valeur de chaque propriété avec `[Parameter]` l' `[CascadingParameter]` attribut ou ayant une valeur correspondante dans le `ParameterView`. Les paramètres qui n’ont pas de valeur `ParameterView` correspondante dans ne sont pas modifiés.

Si `base.SetParametersAync` n’est pas appelé, le code personnalisé peut interpréter la valeur des paramètres entrants de la façon requise. Par exemple, il n’est pas nécessaire d’assigner les paramètres entrants aux propriétés de la classe.

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .

### <a name="after-parameters-are-set"></a>Une fois les paramètres définis

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> sont appelées :

* Lorsque le composant est initialisé et a reçu son premier jeu de paramètres de son composant parent.
* Lorsque le composant parent est à nouveau rendu et fournit :
  * Seuls les types immuables primitifs connus dont au moins un paramètre a été modifié.
  * Tous les paramètres de type complexe. L’infrastructure ne peut pas savoir si les valeurs d’un paramètre de type complexe ont muté en interne, donc elle traite le jeu de paramètres comme étant modifié.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Un travail asynchrone lors de l’application de paramètres et de valeurs `OnParametersSetAsync` de propriété doit se produire pendant l’événement de cycle de vie.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .

### <a name="after-component-render"></a>Après le rendu du composant

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>et <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sont appelées après la fin d’un rendu d’un composant. Les références d’élément et de composant sont remplies à ce stade. Utilisez cette étape pour effectuer des étapes d’initialisation supplémentaires à l’aide du contenu rendu, par exemple l’activation de bibliothèques JavaScript tierces qui opèrent sur les éléments DOM rendus.

Le `firstRender` paramètre pour `OnAfterRenderAsync` et `OnAfterRender`:

* A `true` la valeur la première fois que l’instance de composant est restituée.
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
> Le travail asynchrone immédiatement après le rendu doit se `OnAfterRenderAsync` produire pendant l’événement de cycle de vie.
>
> Même si vous retournez un <xref:System.Threading.Tasks.Task> à `OnAfterRenderAsync`partir de, l’infrastructure ne planifie pas un cycle de rendu supplémentaire pour votre composant une fois cette tâche terminée. Cela permet d’éviter une boucle de rendu infinie. Elle est différente des autres méthodes de cycle de vie, qui planifient un cycle de rendu supplémentaire une fois que la tâche retournée est terminée.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`et `OnAfterRenderAsync` *ne sont pas appelés lors du prérendu sur le serveur.*

Si des gestionnaires d’événements sont configurés, décrochez-les lors de la suppression. Pour plus d’informations, consultez la section [Suppression de composant avec IDisposable](#component-disposal-with-idisposable) .

### <a name="suppress-ui-refreshing"></a>Supprimer l’actualisation de l’interface utilisateur

Substituez <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pour supprimer l’actualisation de l’interface utilisateur. Si l’implémentation retourne `true`, l’interface utilisateur est actualisée :

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`est appelé chaque fois que le composant est restitué.

Même si `ShouldRender` est substitué, le composant est toujours restitué initialement.

## <a name="state-changes"></a>Changements d'état

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>notifie le composant que son état a changé. Le cas échéant, `StateHasChanged` l’appel de entraîne le rerendu du composant.

## <a name="handle-incomplete-async-actions-at-render"></a>Gérer les actions asynchrones incomplètes au rendu

Les actions asynchrones exécutées dans des événements de cycle de vie peuvent ne pas être terminées avant le rendu du composant. Les objets peuvent `null` être ou être remplis de façon incomplète avec des données pendant l’exécution de la méthode Lifecycle. Fournissez une logique de rendu pour confirmer que les objets sont initialisés. Affichez les éléments d’interface utilisateur d’espace réservé (par exemple, un `null`message de chargement) tandis que les objets sont.

Dans le `FetchData` composant des Blazor modèles, `OnInitializedAsync` est remplacé par asynchrone recevoir les données de prévision (`forecasts`). Lorsque `forecasts` a `null`la valeur, un message de chargement est affiché à l’utilisateur. Une fois `Task` la retournée `OnInitializedAsync` terminée, le composant est rerendu avec l’État mis à jour.

*Pages/fetchData. Razor* dans le Blazor modèle de serveur :

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Suppression de composant avec IDisposable

Si un composant implémente <xref:System.IDisposable>, la [méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose) est appelée lorsque le composant est supprimé de l’interface utilisateur. Le composant suivant utilise `@implements IDisposable` et la `Dispose` méthode :

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
> L' <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> appel `Dispose` de dans n’est pas pris en charge. `StateHasChanged`peut être appelé dans le cadre du détachement du convertisseur, donc demander des mises à jour de l’interface utilisateur à ce stade n’est pas pris en charge.

Annule l’abonnement des gestionnaires d’événements des événements .NET. Les exemples de [ Blazor formulaires](xref:blazor/forms-validation) suivants montrent comment décrocher un gestionnaire d’événements dans `Dispose` la méthode :

* Approche de champ privé et lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Approche de la méthode privée

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Gérer les erreurs

Pour plus d’informations sur la gestion des erreurs lors de <xref:blazor/handle-errors#lifecycle-methods>l’exécution de la méthode de cycle de vie, consultez.

## <a name="stateful-reconnection-after-prerendering"></a>Reconnexion avec état après le prérendu

Dans une Blazor application serveur quand `RenderMode` est `ServerPrerendered`, le composant est initialement restitué de manière statique dans le cadre de la page. Une fois que le navigateur a établi une connexion au serveur, le composant est *à nouveau*rendu et le composant est maintenant interactif. Si la méthode de cycle de vie [OnInitialized {Async}](#component-initialization-methods) pour initialiser le composant est présente, la méthode est exécutée *deux fois*:

* Lorsque le composant est prérendu statiquement.
* Après l’établissement de la connexion au serveur.

Cela peut entraîner une modification notable des données affichées dans l’interface utilisateur lorsque le composant est finalement restitué.

Pour éviter le double rendu du scénario dans une Blazor application serveur :

* Transmettez un identificateur qui peut être utilisé pour mettre en cache l’État pendant le prérendu et pour récupérer l’état après le redémarrage de l’application.
* Utilisez l’identificateur pendant le prérendu pour enregistrer l’état du composant.
* Utilisez l’identificateur après le prérendu pour récupérer l’État mis en cache.

Le code suivant illustre une mise `WeatherForecastService` à jour dans une application Blazor serveur basée sur un modèle qui évite le double rendu :

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

Pour plus d’informations sur `RenderMode`le, <xref:blazor/hosting-model-configuration#render-mode>consultez.

## <a name="detect-when-the-app-is-prerendering"></a>Détecter quand l’application est prérendu

[!INCLUDE[](~/includes/blazor-prerendering.md)]
