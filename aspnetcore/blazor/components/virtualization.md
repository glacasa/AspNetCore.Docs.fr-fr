---
title: BlazorVirtualisation des composants ASP.net Core
author: guardrex
description: Découvrez comment utiliser la virtualisation de composants dans des Blazor applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
ms.openlocfilehash: 2ba698eaba23fd67617375e5186d40d45d9772fd
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653878"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="904aa-103">BlazorVirtualisation des composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="904aa-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="904aa-104">Par [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="904aa-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="904aa-105">Améliorez les performances perçues du rendu des composants à l’aide de la Blazor prise en charge intégrée de la virtualisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="904aa-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="904aa-106">La virtualisation est une technique qui permet de limiter le rendu de l’interface utilisateur uniquement aux parties qui sont actuellement visibles.</span><span class="sxs-lookup"><span data-stu-id="904aa-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="904aa-107">Par exemple, la virtualisation est utile lorsque l’application doit afficher une longue liste d’éléments et que seul un sous-ensemble d’éléments doit être visible à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="904aa-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="904aa-108">Blazor fournit le `Virtualize` composant qui peut être utilisé pour ajouter la virtualisation aux composants d’une application.</span><span class="sxs-lookup"><span data-stu-id="904aa-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="904aa-109">Sans virtualisation, une liste standard peut utiliser une boucle C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) pour afficher chaque élément de la liste :</span><span class="sxs-lookup"><span data-stu-id="904aa-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="904aa-110">Si la liste contient des milliers d’éléments, le rendu de la liste peut prendre beaucoup de temps.</span><span class="sxs-lookup"><span data-stu-id="904aa-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="904aa-111">L’utilisateur peut rencontrer un décalage de l’interface utilisateur notable.</span><span class="sxs-lookup"><span data-stu-id="904aa-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="904aa-112">Au lieu de restituer tous les éléments de la liste en même temps, remplacez la [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle par le `Virtualize` composant et spécifiez une source d’élément fixe avec `Items` .</span><span class="sxs-lookup"><span data-stu-id="904aa-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="904aa-113">Seuls les éléments qui sont actuellement visibles sont rendus :</span><span class="sxs-lookup"><span data-stu-id="904aa-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="904aa-114">Si vous ne spécifiez pas de contexte pour le composant avec `Context` , utilisez la `context` valeur ( `@context.{PROPERTY}` ) dans le modèle de contenu de l’élément :</span><span class="sxs-lookup"><span data-stu-id="904aa-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="904aa-115">Le `Virtualize` composant calcule le nombre d’éléments à afficher en fonction de la hauteur du conteneur et de la taille des éléments rendus.</span><span class="sxs-lookup"><span data-stu-id="904aa-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="904aa-116">Le contenu de l’élément pour le `Virtualize` composant peut inclure les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="904aa-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="904aa-117">Code HTML et Razor code brut, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="904aa-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="904aa-118">Un ou plusieurs Razor composants.</span><span class="sxs-lookup"><span data-stu-id="904aa-118">One or more Razor components.</span></span>
* <span data-ttu-id="904aa-119">Combinaison de composants HTML/ Razor et Razor .</span><span class="sxs-lookup"><span data-stu-id="904aa-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="904aa-120">Délégué du fournisseur d’éléments</span><span class="sxs-lookup"><span data-stu-id="904aa-120">Item provider delegate</span></span>

<span data-ttu-id="904aa-121">Si vous ne souhaitez pas charger tous les éléments dans la mémoire, vous pouvez spécifier une méthode déléguée du fournisseur items pour le paramètre du composant `ItemsProvider` qui récupère de manière asynchrone les éléments demandés à la demande :</span><span class="sxs-lookup"><span data-stu-id="904aa-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="904aa-122">Le fournisseur items reçoit un `ItemsProviderRequest` , qui spécifie le nombre d’éléments requis commençant à un index de début spécifique.</span><span class="sxs-lookup"><span data-stu-id="904aa-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="904aa-123">Le fournisseur d’éléments récupère ensuite les éléments demandés à partir d’une base de données ou d’un autre service et les retourne sous la forme d’un, ainsi que du `ItemsProviderResult<TItem>` nombre total d’éléments.</span><span class="sxs-lookup"><span data-stu-id="904aa-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="904aa-124">Le fournisseur d’éléments peut choisir de récupérer les éléments avec chaque demande ou de les mettre en cache afin qu’ils soient facilement disponibles.</span><span class="sxs-lookup"><span data-stu-id="904aa-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="904aa-125">N’essayez pas d’utiliser un fournisseur d’éléments et assignez une collection à `Items` pour le même `Virtualize` composant.</span><span class="sxs-lookup"><span data-stu-id="904aa-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="904aa-126">L’exemple suivant charge des employés à partir d’un `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="904aa-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="904aa-127">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="904aa-127">Placeholder</span></span>

<span data-ttu-id="904aa-128">Étant donné que la demande d’éléments à partir d’une source de données distante peut prendre un certain temps, vous avez la possibilité d’afficher un espace réservé ( `<Placeholder>...</Placeholder>` ) jusqu’à ce que les données de l’élément soient disponibles :</span><span class="sxs-lookup"><span data-stu-id="904aa-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="904aa-129">Taille de l’élément</span><span class="sxs-lookup"><span data-stu-id="904aa-129">Item size</span></span>

<span data-ttu-id="904aa-130">La taille de chaque élément en pixels peut être définie avec `ItemSize` (valeur par défaut : 50px) :</span><span class="sxs-lookup"><span data-stu-id="904aa-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="904aa-131">Nombre de suranalyses</span><span class="sxs-lookup"><span data-stu-id="904aa-131">Overscan count</span></span>

<span data-ttu-id="904aa-132">`OverscanCount` détermine le nombre d’éléments supplémentaires qui sont rendus avant et après la zone visible.</span><span class="sxs-lookup"><span data-stu-id="904aa-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="904aa-133">Ce paramètre permet de réduire la fréquence de rendu lors du défilement.</span><span class="sxs-lookup"><span data-stu-id="904aa-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="904aa-134">Toutefois, des valeurs plus élevées entraînent le rendu d’un plus grand nombre d’éléments dans la page (valeur par défaut : 3) :</span><span class="sxs-lookup"><span data-stu-id="904aa-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="904aa-135">Par exemple, une grille ou une liste qui restitue des centaines de lignes contenant des composants nécessite une utilisation intensive du processeur pour le rendu.</span><span class="sxs-lookup"><span data-stu-id="904aa-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="904aa-136">Envisagez de virtualiser une disposition de grille ou de liste afin que seul un sous-ensemble des composants soit rendu à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="904aa-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span>

<span data-ttu-id="904aa-137">Le `Virtualize` composant suivant ( `Virtualize.cs` ) implémente <xref:Microsoft.AspNetCore.Components.ComponentBase> pour restituer le contenu enfant en fonction du défilement de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="904aa-137">The following `Virtualize` component (`Virtualize.cs`) implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render child content based on user scrolling:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Rendering;
using Microsoft.JSInterop;

public class Virtualize<TItem> : ComponentBase
{
    [Parameter]
    public string TagName { get; set; } = "div";

    [Parameter]
    public RenderFragment<TItem> ChildContent { get; set; }

    [Parameter]
    public ICollection<TItem> Items { get; set; }

    [Parameter]
    public double ItemHeight { get; set; }

    [Parameter(CaptureUnmatchedValues = true)] 
    public Dictionary<string, object> Attributes { get; set; }

    [Inject]
    IJSRuntime JS { get; set; }

    ElementReference contentElement;
    int numItemsToSkipBefore;
    int numItemsToShow;

    protected override void BuildRenderTree(RenderTreeBuilder builder)
    {
        // Render actual content
        builder.OpenElement(0, TagName);
        builder.AddMultipleAttributes(1, Attributes);

        var translateY = numItemsToSkipBefore * ItemHeight;
        builder.AddAttribute(2, "style", $"transform: translateY({ translateY }px);");
        builder.AddAttribute(2, "data-translateY", translateY);
        builder.AddElementReferenceCapture(3, @ref => { contentElement = @ref; });

        // As an important optimization, *don't* use builder.AddContent(seq, ChildContent, item)
        // because that implicitly wraps a new region around each item, which in turn means that 
        // @key does nothing (because keys are scoped to regions). Instead, create a single 
        // container region and then invoke the fragments directly.

        builder.OpenRegion(4);

        foreach (var item in Items.Skip(numItemsToSkipBefore).Take(numItemsToShow))
        {
            ChildContent(item)(builder);
        }

        builder.CloseRegion();

        builder.CloseElement();

        // Also emit a spacer that causes the total vertical height to add up to 
        // Items.Count()*numItems

        builder.OpenElement(5, "div");
        var numHiddenItems = Items.Count - numItemsToShow;
        builder.AddAttribute(6, "style", 
            $"width: 1px; height: { numHiddenItems * ItemHeight }px;");
        builder.CloseElement();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            var objectRef = DotNetObjectReference.Create(this);
            var initResult = await JS.InvokeAsync<ScrollEventArgs>(
                "VirtualizedComponent._initialize", objectRef, contentElement);
            OnScroll(initResult);
        }
    }

    [JSInvokable]
    public void OnScroll(ScrollEventArgs args)
    {
        var relativeTop = args.ContainerRect.Top - args.ContentRect.Top;
        numItemsToSkipBefore = Math.Max(0, (int)(relativeTop / ItemHeight));

        var visibleHeight = args.ContainerRect.Bottom - 
            (args.ContentRect.Top + numItemsToSkipBefore * ItemHeight);
        numItemsToShow = (int)Math.Ceiling(visibleHeight / ItemHeight) + 3;

        StateHasChanged();
    }

    public class ScrollEventArgs
    {
        public DOMRect ContainerRect { get; set; }
        public DOMRect ContentRect { get; set; }
    }

    public class DOMRect
    {
        public double Top { get; set; }
        public double Bottom { get; set; }
        public double Left { get; set; } 
        public double Right { get; set; }
        public double Width { get; set; }
        public double Height { get; set; }
    }
}
```

<span data-ttu-id="904aa-138">Le `FetchData` composant suivant ( `FetchData.razor` ) utilise le `Virtualize` composant précédent pour afficher 25 lignes de données météorologiques à la fois :</span><span class="sxs-lookup"><span data-stu-id="904aa-138">The following `FetchData` component (`FetchData.razor`) uses the preceding `Virtualize` component to display 25 rows of weather data at a time:</span></span>

```razor
@page "/"
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from a service.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h2>Using <code>table-layout: fixed</code></h2>
    <div style="height:300px; overflow-y: auto;">
        <Virtualize ItemHeight="25" Items="@forecasts">
            <tr @key="@context.GetHashCode()" 
                    style="display: table; table-layout: fixed; width: 100%;">
                <td>@context.Date.ToShortDateString()</td>
                <td>@context.TemperatureC</td>
                <td>@context.TemperatureF</td>
                <td>@context.Summary</td>
            </tr>
        </Virtualize>
    </div>

    <h2>Using <code>position: sticky</code></h2>
    <div style="height: 300px; overflow-y: auto; position: relative;">
        <table>
            <thead class="sticky">
                <tr>
                    <th>Date</th>
                    <th>Temperature (C)</th>
                    <th>Temperature (F)</th>
                    <th>Summary</th>
                </tr>
            </thead>

            <Virtualize TagName="tbody" ItemHeight="25" Items="@forecasts">
                <tr @key="@context.GetHashCode()">
                    <td>@context.Date.ToShortDateString()</td>
                    <td>@context.TemperatureC</td>
                    <td>@context.TemperatureF</td>
                    <td>@context.Summary</td>
                </tr>
            </Virtualize>
        </table>
    </div>

    <style type="text/css">
        thead.sticky th {
            position: sticky;
            top: 0;
        }
        tr td {
            height: 25px;
        }
    </style>
}

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "sample-data/weather.json");
    }

    public class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public string Summary { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
    }
}
```

<span data-ttu-id="904aa-139">Dans l’exemple précédent, l’approche consiste à éviter le positionnement absolu pour chaque élément individuel.</span><span class="sxs-lookup"><span data-stu-id="904aa-139">In the preceding example, the approach is about avoiding absolute positioning for each individual item.</span></span> <span data-ttu-id="904aa-140">Le positionnement absolu présente des avantages (nous pouvons nous assurer que les éléments occupent la quantité spécifiée d’espace Y), mais qu’il a l’inconvénient de perdre les largeurs normales et que les colonnes de la table ne peuvent pas être alignées sur les lignes/l’en-tête en fonction du dimensionnement du contenu.</span><span class="sxs-lookup"><span data-stu-id="904aa-140">Absolute positioning would have some advantages (we can be sure the items do take up the specified amount of Y space) but has the bad disadvantage that you lose the normal widths and can't get table columns to line up across rows/header when based on content sizing.</span></span>

<span data-ttu-id="904aa-141">Le concept qui sous-tend la conception du `Virtualize` composant est que le composant ne change pas la manière dont les éléments sont disposés dans le DOM.</span><span class="sxs-lookup"><span data-stu-id="904aa-141">The concept behind the design of the `Virtualize` component is that the component doesn't change how the items are laid out within the DOM.</span></span> <span data-ttu-id="904aa-142">Il n’y a pas d’éléments wrapper ajoutés, en plus de l’élément unique dont `TagName` vous spécifiez.</span><span class="sxs-lookup"><span data-stu-id="904aa-142">There are no added wrapper elements, besides the single one whose `TagName` you specify.</span></span>

<span data-ttu-id="904aa-143">La meilleure approche consiste à éviter même l' `TagName` élément wrapper.</span><span class="sxs-lookup"><span data-stu-id="904aa-143">The best approach is to avoid even the `TagName` wrapper element.</span></span> <span data-ttu-id="904aa-144">Faire en sorte que le `Virtualize` composant n’émette aucun élément propre.</span><span class="sxs-lookup"><span data-stu-id="904aa-144">Have the `Virtualize` component emit no elements of its own.</span></span> <span data-ttu-id="904aa-145">Tout le composant est rendu. Toutefois, un grand nombre d’instances de modèle sont nécessaires pour remplir tout l’espace visible restant dans l’ancêtre le plus proche, et ajouter un élément d’espacement suivant qui fait de l’ancêtre défilant la plage de défilement appropriée.</span><span class="sxs-lookup"><span data-stu-id="904aa-145">All the component does is render however many of the template instances are required to fill up any remaining visible space in the closest scrollable ancestor, plus add a following spacer element that makes the scrollable ancestor have the right scrolling range.</span></span> <span data-ttu-id="904aa-146">En ce qui concerne la disposition, elle est la même que si la plage complète des enfants était physiquement dans le DOM.</span><span class="sxs-lookup"><span data-stu-id="904aa-146">As far as the layout is concerned, it's the same as if the full range of children were physically in the DOM.</span></span> <span data-ttu-id="904aa-147">Toutefois, vous devez spécifier une précision `ItemHeight` .</span><span class="sxs-lookup"><span data-stu-id="904aa-147">It does require you to specify an accurate `ItemHeight` though.</span></span> <span data-ttu-id="904aa-148">Si vous ne parvenez pas à le faire (par exemple, parce que vous êtes confus et que vous pensez qu’il est possible de spécifier `style.height` sur un `<tr>` ), le composant finit par restituer un nombre incorrect d’instances de modèle et ne remplit pas l’interface utilisateur ou n’effectue pas un rendu trop important.</span><span class="sxs-lookup"><span data-stu-id="904aa-148">If you get it wrong (for example, because you're confused and think it's valid to specify `style.height` on a `<tr>`), then the component ends up rendering the wrong number of template instances and either not fill up the UI or inefficiently render too many.</span></span> <span data-ttu-id="904aa-149">En outre, la plage de défilement sur le parent n’est pas correcte.</span><span class="sxs-lookup"><span data-stu-id="904aa-149">Additionally, the scroll range on the parent won't be correct.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="904aa-150">Modifications d'état</span><span class="sxs-lookup"><span data-stu-id="904aa-150">State changes</span></span>

<span data-ttu-id="904aa-151">Lorsque vous apportez des modifications aux éléments restitués par le `Virtualize` composant, appelez <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> pour forcer la réévaluation et le rerendu du composant.</span><span class="sxs-lookup"><span data-stu-id="904aa-151">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
