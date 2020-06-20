---
title: ASP.NET Core Blazor les valeurs et paramètres en cascade
author: guardrex
description: Apprenez à transmettre des données d’un composant ancêtre à des composants descendants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 70f379b3b0e48dbb340f319f3346bbbf44588740
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103737"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="1223e-103">ASP.NET Core Blazor les valeurs et paramètres en cascade</span><span class="sxs-lookup"><span data-stu-id="1223e-103">ASP.NET Core Blazor cascading values and parameters</span></span>

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="1223e-104">Valeurs et paramètres en cascade</span><span class="sxs-lookup"><span data-stu-id="1223e-104">Cascading values and parameters</span></span>

<span data-ttu-id="1223e-105">Dans certains scénarios, il est peu commode de transmettre des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composant](xref:blazor/components/index#component-parameters), en particulier lorsqu’il existe plusieurs couches de composant.</span><span class="sxs-lookup"><span data-stu-id="1223e-105">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="1223e-106">Les valeurs et paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant ancêtre de fournir une valeur à tous ses composants descendants.</span><span class="sxs-lookup"><span data-stu-id="1223e-106">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="1223e-107">Les valeurs et les paramètres en cascade fournissent également une approche pour les composants à coordonner.</span><span class="sxs-lookup"><span data-stu-id="1223e-107">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="1223e-108">Exemple de thème</span><span class="sxs-lookup"><span data-stu-id="1223e-108">Theme example</span></span>

<span data-ttu-id="1223e-109">Dans l’exemple suivant tiré de l’exemple d’application, la `ThemeInfo` classe spécifie les informations de thème pour descendre dans la hiérarchie des composants, de sorte que tous les boutons d’une partie donnée de l’application partagent le même style.</span><span class="sxs-lookup"><span data-stu-id="1223e-109">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="1223e-110">*UIThemeClasses/themeinfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="1223e-110">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="1223e-111">Un composant ancêtre peut fournir une valeur en cascade à l’aide du composant de valeur en cascade.</span><span class="sxs-lookup"><span data-stu-id="1223e-111">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="1223e-112">Le <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant encapsule une sous-arborescence de la hiérarchie des composants et fournit une valeur unique à tous les composants de cette sous-arborescence.</span><span class="sxs-lookup"><span data-stu-id="1223e-112">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="1223e-113">Par exemple, l’exemple d’application spécifie les informations de thème ( `ThemeInfo` ) dans l’une des dispositions de l’application en tant que paramètre en cascade pour tous les composants qui composent le corps de la disposition de la `@Body` propriété.</span><span class="sxs-lookup"><span data-stu-id="1223e-113">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="1223e-114">`ButtonClass`la valeur est affectée `btn-success` à dans le composant Layout.</span><span class="sxs-lookup"><span data-stu-id="1223e-114">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="1223e-115">Tout composant descendant peut consommer cette propriété par le biais de l' `ThemeInfo` objet en cascade.</span><span class="sxs-lookup"><span data-stu-id="1223e-115">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="1223e-116">`CascadingValuesParametersLayout`-</span><span class="sxs-lookup"><span data-stu-id="1223e-116">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="1223e-117">Pour utiliser des valeurs en cascade, les composants déclarent des paramètres en cascade à l’aide de l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="1223e-117">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="1223e-118">Les valeurs en cascade sont liées aux paramètres en cascade par type.</span><span class="sxs-lookup"><span data-stu-id="1223e-118">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="1223e-119">Dans l’exemple d’application, le `CascadingValuesParametersTheme` composant lie la `ThemeInfo` valeur en cascade à un paramètre en cascade.</span><span class="sxs-lookup"><span data-stu-id="1223e-119">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="1223e-120">Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="1223e-120">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="1223e-121">`CascadingValuesParametersTheme`-</span><span class="sxs-lookup"><span data-stu-id="1223e-121">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="1223e-122">Pour mettre en cascade plusieurs valeurs du même type dans la même sous-arborescence, fournissez une <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> chaîne unique à chaque <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant et à son [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut correspondant.</span><span class="sxs-lookup"><span data-stu-id="1223e-122">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="1223e-123">Dans l’exemple suivant, deux <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composants montent en cascade différentes instances de `MyCascadingType` par nom :</span><span class="sxs-lookup"><span data-stu-id="1223e-123">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="1223e-124">Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans le composant ancêtre par nom :</span><span class="sxs-lookup"><span data-stu-id="1223e-124">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="1223e-125">Exemple TabSet</span><span class="sxs-lookup"><span data-stu-id="1223e-125">TabSet example</span></span>

<span data-ttu-id="1223e-126">Les paramètres en cascade permettent également aux composants de collaborer au sein de la hiérarchie des composants.</span><span class="sxs-lookup"><span data-stu-id="1223e-126">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="1223e-127">Par exemple, considérez l’exemple *TabSet* suivant dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="1223e-127">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="1223e-128">L’exemple d’application possède une `ITab` interface qui implémente les onglets suivants :</span><span class="sxs-lookup"><span data-stu-id="1223e-128">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="1223e-129">Le `CascadingValuesParametersTabSet` composant utilise le `TabSet` composant, qui contient plusieurs `Tab` composants :</span><span class="sxs-lookup"><span data-stu-id="1223e-129">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="1223e-130">Les `Tab` composants enfants ne sont pas explicitement passés comme paramètres à `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="1223e-130">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="1223e-131">Au lieu de cela, les `Tab` composants enfants font partie du contenu enfant de `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="1223e-131">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="1223e-132">Toutefois, le `TabSet` doit toujours connaître chaque `Tab` composant pour pouvoir afficher les en-têtes et l’onglet actif. Pour activer cette coordination sans nécessiter de code supplémentaire, le `TabSet` composant *peut se présenter comme une valeur en cascade* qui est ensuite récupérée par les `Tab` composants descendants.</span><span class="sxs-lookup"><span data-stu-id="1223e-132">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="1223e-133">`TabSet`-</span><span class="sxs-lookup"><span data-stu-id="1223e-133">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="1223e-134">Les composants descendants `Tab` capturent le contenant `TabSet` comme paramètre en cascade, de sorte que les `Tab` composants s’ajoutent eux-mêmes à la et à la `TabSet` coordonnée sur laquelle l’onglet est actif.</span><span class="sxs-lookup"><span data-stu-id="1223e-134">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="1223e-135">`Tab`-</span><span class="sxs-lookup"><span data-stu-id="1223e-135">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]