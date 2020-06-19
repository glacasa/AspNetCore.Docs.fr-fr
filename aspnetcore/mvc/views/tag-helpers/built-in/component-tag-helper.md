---
title: Tag Helper Component dans ASP.NET Core
author: guardrex
ms.author: riande
description: Découvrez comment utiliser le tag Helper du composant ASP.NET Core pour afficher les Razor composants dans les pages et les vues.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: df978d49201ba1010ddf13b1b9a63ae27116616e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103086"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="006e0-103">Tag Helper Component dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="006e0-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="006e0-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="006e0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="006e0-105">Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper Component](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="006e0-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="006e0-106">Prérequis</span><span class="sxs-lookup"><span data-stu-id="006e0-106">Prerequisites</span></span>

<span data-ttu-id="006e0-107">Suivez les instructions de la section *préparer l’application à utiliser les composants des pages et des vues* de l' <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span><span class="sxs-lookup"><span data-stu-id="006e0-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="006e0-108">Tag Helper Component</span><span class="sxs-lookup"><span data-stu-id="006e0-108">Component Tag Helper</span></span>

<span data-ttu-id="006e0-109">Le tag Helper Component suivant restitue le `Counter` composant dans une page ou une vue :</span><span class="sxs-lookup"><span data-stu-id="006e0-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="006e0-110">L’exemple précédent suppose que le `Counter` composant se trouve dans le dossier *pages* de l’application.</span><span class="sxs-lookup"><span data-stu-id="006e0-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="006e0-111">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using BlazorSample.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="006e0-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages`).</span></span>

<span data-ttu-id="006e0-112">Le tag Helper Component peut également transmettre des paramètres à des composants.</span><span class="sxs-lookup"><span data-stu-id="006e0-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="006e0-113">Prenons le `ColorfulCheckbox` composant suivant qui définit la couleur et la taille de l’étiquette de case à cocher :</span><span class="sxs-lookup"><span data-stu-id="006e0-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="006e0-114">Les `Size` `int` paramètres du composant () et `Color` ( `string` ) peuvent être définis par le tag Helper du composant : [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="006e0-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="006e0-115">L’exemple précédent suppose que le `ColorfulCheckbox` composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="006e0-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="006e0-116">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="006e0-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="006e0-117">Le code HTML suivant est affiché dans la page ou la vue :</span><span class="sxs-lookup"><span data-stu-id="006e0-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="006e0-118">Le passage d’une chaîne entre guillemets requiert une [ Razor expression explicite](xref:mvc/views/razor#explicit-razor-expressions), comme indiqué `param-Color` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="006e0-118">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="006e0-119">Le Razor comportement d’analyse d’une `string` valeur de type ne s’applique pas à un `param-*` attribut, car l’attribut est un `object` type.</span><span class="sxs-lookup"><span data-stu-id="006e0-119">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="006e0-120">Le type de paramètre doit être sérialisable JSON, ce qui signifie généralement que le type doit avoir un constructeur par défaut et des propriétés définissables.</span><span class="sxs-lookup"><span data-stu-id="006e0-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="006e0-121">Par exemple, vous pouvez spécifier une valeur pour `Size` et `Color` dans l’exemple précédent, car les types de `Size` et `Color` sont des types primitifs ( `int` et `string` ), qui sont pris en charge par le sérialiseur JSON.</span><span class="sxs-lookup"><span data-stu-id="006e0-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="006e0-122">Dans l’exemple suivant, un objet de classe est passé au composant :</span><span class="sxs-lookup"><span data-stu-id="006e0-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="006e0-123">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="006e0-123">*MyClass.cs*:</span></span>

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

<span data-ttu-id="006e0-124">**La classe doit avoir un constructeur sans paramètre public.**</span><span class="sxs-lookup"><span data-stu-id="006e0-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="006e0-125">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="006e0-125">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="006e0-126">*Pages/MyPage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="006e0-126">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="006e0-127">L’exemple précédent suppose que le `MyComponent` composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="006e0-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="006e0-128">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `@using BlazorSample` et `@using BlazorSample.Shared` ).</span><span class="sxs-lookup"><span data-stu-id="006e0-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="006e0-129">`MyClass`se trouve dans l’espace de noms de l’application.</span><span class="sxs-lookup"><span data-stu-id="006e0-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="006e0-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="006e0-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="006e0-131">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="006e0-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="006e0-132">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="006e0-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="006e0-133">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="006e0-133">Render Mode</span></span> | <span data-ttu-id="006e0-134">Description</span><span class="sxs-lookup"><span data-stu-id="006e0-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="006e0-135">Génère le rendu du composant en HTML statique et comprend un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="006e0-135">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="006e0-136">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="006e0-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="006e0-137">Restitue un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="006e0-137">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="006e0-138">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="006e0-138">Output from the component isn't included.</span></span> <span data-ttu-id="006e0-139">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="006e0-139">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="006e0-140">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="006e0-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="006e0-141">Alors que les pages et les vues peuvent utiliser des composants, la réciproque n’est pas vraie.</span><span class="sxs-lookup"><span data-stu-id="006e0-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="006e0-142">Les composants ne peuvent pas utiliser les fonctionnalités spécifiques aux vues et aux pages, telles que les vues partielles et les sections.</span><span class="sxs-lookup"><span data-stu-id="006e0-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="006e0-143">Pour utiliser la logique d’une vue partielle dans un composant, factorisez la logique de la vue partielle dans un composant.</span><span class="sxs-lookup"><span data-stu-id="006e0-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="006e0-144">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="006e0-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="006e0-145">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="006e0-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
