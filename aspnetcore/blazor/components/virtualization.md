---
title: BlazorVirtualisation des composants ASP.net Core
author: guardrex
description: Découvrez comment utiliser la virtualisation de composants dans des Blazor applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/virtualization
ms.openlocfilehash: 911eeeb445741aa1519e1464dd4a75e26f6f12ab
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847570"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorVirtualisation des composants ASP.net Core

Par [Daniel Roth](https://github.com/danroth27)

Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge intégrée de la virtualisation du Framework. La virtualisation est une technique qui permet de limiter le rendu de l’interface utilisateur uniquement aux parties qui sont actuellement visibles. Par exemple, la virtualisation est utile lorsque l’application doit restituer une longue liste ou une table avec de nombreuses lignes et que seul un sous-ensemble d’éléments doit être visible à un moment donné. Blazor fournit le `Virtualize` composant qui peut être utilisé pour ajouter la virtualisation aux composants d’une application.

::: moniker range=">= aspnetcore-5.0"

Sans virtualisation, une liste standard ou un composant basé sur une table peut utiliser une [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle C# pour afficher chaque élément de la liste ou chaque ligne de la table :

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

Si la liste contient des milliers d’éléments, le rendu de la liste peut prendre beaucoup de temps. L’utilisateur peut rencontrer un décalage de l’interface utilisateur notable.

Au lieu de restituer tous les éléments de la liste en même temps, remplacez la [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle par le `Virtualize` composant et spécifiez une source d’élément fixe avec `Items` . Seuls les éléments qui sont actuellement visibles sont rendus :

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

Si vous ne spécifiez pas de contexte pour le composant avec `Context` , utilisez la `context` valeur ( `@context.{PROPERTY}` ) dans le modèle de contenu de l’élément :

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

Le `Virtualize` composant calcule le nombre d’éléments à afficher en fonction de la hauteur du conteneur et de la taille des éléments rendus.

## <a name="item-provider-delegate"></a>Délégué du fournisseur d’éléments

Si vous ne souhaitez pas charger tous les éléments dans la mémoire, vous pouvez spécifier une méthode déléguée du fournisseur items pour le paramètre du composant `ItemsProvider` qui récupère de manière asynchrone les éléments demandés à la demande :

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

Le fournisseur items reçoit un `ItemsProviderRequest` , qui spécifie le nombre d’éléments requis commençant à un index de début spécifique. Le fournisseur d’éléments récupère ensuite les éléments demandés à partir d’une base de données ou d’un autre service et les retourne sous la forme d’un, ainsi que du `ItemsProviderResult<TItem>` nombre total d’éléments. Le fournisseur d’éléments peut choisir de récupérer les éléments avec chaque demande ou de les mettre en cache afin qu’ils soient facilement disponibles. N’essayez pas d’utiliser un fournisseur d’éléments et assignez une collection à `Items` pour le même `Virtualize` composant.

L’exemple suivant charge des employés à partir d’un `EmployeeService` :

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a>Espace réservé

Étant donné que la demande d’éléments à partir d’une source de données distante peut prendre un certain temps, vous avez la possibilité d’afficher un espace réservé ( `<Placeholder>...</Placeholder>` ) jusqu’à ce que les données de l’élément soient disponibles :

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a>Taille de l’élément

La taille de chaque élément en pixels peut être définie avec `ItemSize` (valeur par défaut : 50px) :

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a>Nombre de suranalyses

`OverscanCount` détermine le nombre d’éléments supplémentaires qui sont rendus avant et après la zone visible. Ce paramètre permet de réduire la fréquence de rendu lors du défilement. Toutefois, des valeurs plus élevées entraînent le rendu d’un plus grand nombre d’éléments dans la page (valeur par défaut : 3) :

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Par exemple, une grille ou une liste qui restitue des centaines de lignes contenant des composants nécessite une utilisation intensive du processeur pour le rendu. Envisagez de virtualiser une disposition de grille ou de liste afin que seul un sous-ensemble des composants soit rendu à un moment donné. Pour obtenir un exemple de rendu de sous-ensemble de composants, consultez les composants suivants dans l' [ `Virtualization` exemple d’application (référentiel GitHub ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ) : composant écrit en C# qui implémente <xref:Microsoft.AspNetCore.Components.ComponentBase> pour afficher un ensemble de lignes de données météorologiques en fonction du défilement de l’utilisateur.
* `FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ) : utilise le `Virtualize` composant pour afficher 25 lignes de données météorologiques à la fois.

::: moniker-end

## <a name="state-changes"></a>Modifications d'état

Lorsque vous apportez des modifications aux éléments restitués par le `Virtualize` composant, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour forcer la réévaluation et le rerendu du composant.
