---
title: Tag Helper Component dans ASP.NET Core
author: guardrex
ms.author: riande
description: Découvrez comment utiliser le tag Helper du composant ASP.NET Core pour afficher Razor les composants dans les pages et les vues.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773927"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="6140f-103">Tag Helper Component dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6140f-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="6140f-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6140f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6140f-105">Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper Component](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="6140f-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6140f-106">Prérequis</span><span class="sxs-lookup"><span data-stu-id="6140f-106">Prerequisites</span></span>

<span data-ttu-id="6140f-107">Suivez les instructions de la section *préparer l’application à utiliser les composants des pages et* des vues <xref:blazor/integrate-components#prepare-the-app> de l’article.</span><span class="sxs-lookup"><span data-stu-id="6140f-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="6140f-108">Tag Helper Component</span><span class="sxs-lookup"><span data-stu-id="6140f-108">Component Tag Helper</span></span>

<span data-ttu-id="6140f-109">Le tag Helper Component suivant restitue le `Counter` composant dans une page ou une vue :</span><span class="sxs-lookup"><span data-stu-id="6140f-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="6140f-110">L’exemple précédent suppose que le `Counter` composant se trouve dans le dossier *pages* de l’application.</span><span class="sxs-lookup"><span data-stu-id="6140f-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="6140f-111">Le tag Helper Component peut également transmettre des paramètres à des composants.</span><span class="sxs-lookup"><span data-stu-id="6140f-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="6140f-112">Prenons le composant `ColorfulCheckbox` suivant qui définit la couleur et la taille de l’étiquette de case à cocher :</span><span class="sxs-lookup"><span data-stu-id="6140f-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="6140f-113">Les `Size` paramètres`int`du `Color` [composant](xref:blazor/components#component-parameters) (`string`) et () peuvent être définis par le tag Helper du composant :</span><span class="sxs-lookup"><span data-stu-id="6140f-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="6140f-114">L’exemple précédent suppose que le `ColorfulCheckbox` composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="6140f-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="6140f-115">Le code HTML suivant est affiché dans la page ou la vue :</span><span class="sxs-lookup"><span data-stu-id="6140f-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="6140f-116">Le passage d’une chaîne entre guillemets requiert une [expression Razor explicite](xref:mvc/views/razor#explicit-razor-expressions), comme illustré `param-Color` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="6140f-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="6140f-117">Le comportement d’analyse Razor pour une `string` valeur de type ne s’applique `param-*` pas à un attribut, car `object` l’attribut est un type.</span><span class="sxs-lookup"><span data-stu-id="6140f-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="6140f-118">Le type de paramètre doit être sérialisable JSON, ce qui signifie généralement que le type doit avoir un constructeur par défaut et des propriétés définissables.</span><span class="sxs-lookup"><span data-stu-id="6140f-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="6140f-119">Par exemple, vous pouvez spécifier une valeur pour `Size` et `Color` dans l’exemple précédent, car les types `Size` de `Color` et sont des types`int` primitifs (et `string`), qui sont pris en charge par le sérialiseur JSON.</span><span class="sxs-lookup"><span data-stu-id="6140f-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="6140f-120">Dans l’exemple suivant, un objet de classe est passé au composant :</span><span class="sxs-lookup"><span data-stu-id="6140f-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="6140f-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="6140f-121">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="6140f-122">**La classe doit avoir un constructeur sans paramètre public.**</span><span class="sxs-lookup"><span data-stu-id="6140f-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="6140f-123">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6140f-123">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="6140f-124">*Pages/MyPage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6140f-124">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="6140f-125">L’exemple précédent suppose que le `MyComponent` composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="6140f-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="6140f-126">`MyClass`se trouve dans l’espace de noms`{APP ASSEMBLY}`de l’application ().</span><span class="sxs-lookup"><span data-stu-id="6140f-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="6140f-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="6140f-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="6140f-128">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="6140f-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="6140f-129">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une application éblouissant à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6140f-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="6140f-130">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="6140f-130">Render Mode</span></span> | <span data-ttu-id="6140f-131">Description</span><span class="sxs-lookup"><span data-stu-id="6140f-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="6140f-132">Génère le rendu du composant en HTML statique et comprend un marqueur Blazor pour une application serveur.</span><span class="sxs-lookup"><span data-stu-id="6140f-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="6140f-133">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="6140f-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="6140f-134">Restitue un marqueur pour Blazor une application serveur.</span><span class="sxs-lookup"><span data-stu-id="6140f-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="6140f-135">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="6140f-135">Output from the component isn't included.</span></span> <span data-ttu-id="6140f-136">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="6140f-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="6140f-137">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="6140f-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="6140f-138">Alors que les pages et les vues peuvent utiliser des composants, la réciproque n’est pas vraie.</span><span class="sxs-lookup"><span data-stu-id="6140f-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="6140f-139">Les composants ne peuvent pas utiliser les fonctionnalités spécifiques aux vues et aux pages, telles que les vues partielles et les sections.</span><span class="sxs-lookup"><span data-stu-id="6140f-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="6140f-140">Pour utiliser la logique d’une vue partielle dans un composant, factorisez la logique de la vue partielle dans un composant.</span><span class="sxs-lookup"><span data-stu-id="6140f-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="6140f-141">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="6140f-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6140f-142">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6140f-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
