---
title: Aide à l’étiquette de composant dans ASP.NET noyau
author: guardrex
ms.author: riande
description: Apprenez à utiliser le ASP.NET Core Component Tag Helper pour rendre les composants Razor dans les pages et les vues.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123428"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="dc8d1-103">Aide à l’étiquette de composant dans ASP.NET noyau</span><span class="sxs-lookup"><span data-stu-id="dc8d1-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="dc8d1-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dc8d1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dc8d1-105">Pour rendre un composant à partir d’une page ou d’une vue, utilisez [l’aide à l’étiquette de composant](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="dc8d1-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dc8d1-106">Prérequis</span><span class="sxs-lookup"><span data-stu-id="dc8d1-106">Prerequisites</span></span>

<span data-ttu-id="dc8d1-107">Suivez les conseils de *l’application Préparer à utiliser* des <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> composants dans les pages et les vues de l’article.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="dc8d1-108">Aide à l’étiquette de composant</span><span class="sxs-lookup"><span data-stu-id="dc8d1-108">Component Tag Helper</span></span>

<span data-ttu-id="dc8d1-109">L’aide à l’étiquette `Counter` de composant suivante rend le composant dans une page ou une vue :</span><span class="sxs-lookup"><span data-stu-id="dc8d1-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="dc8d1-110">L’exemple précédent suppose `Counter` que le composant se trouve dans le dossier *Pages* de l’application.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="dc8d1-111">L’aide à l’étiquette de composant peut également passer des paramètres aux composants.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="dc8d1-112">Considérez `ColorfulCheckbox` le composant suivant qui définit la couleur et la taille de l’étiquette de la case à cocher :</span><span class="sxs-lookup"><span data-stu-id="dc8d1-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="dc8d1-113">Les `Size` `int`paramètres `Color` des`string`composants ( ) et ) peuvent être [définis](xref:blazor/components#component-parameters) par l’aide à l’étiquette des composants :</span><span class="sxs-lookup"><span data-stu-id="dc8d1-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="dc8d1-114">L’exemple précédent suppose `ColorfulCheckbox` que le composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="dc8d1-115">Le HTML suivant est rendu dans la page ou la vue:</span><span class="sxs-lookup"><span data-stu-id="dc8d1-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="dc8d1-116">Passer une chaîne citée nécessite une expression `param-Color` explicite [Razor](xref:mvc/views/razor#explicit-razor-expressions), comme indiqué dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="dc8d1-117">Le comportement d’analyse `string` Razor pour une valeur `param-*` de type ne `object` s’applique pas à un attribut parce que l’attribut est un type.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="dc8d1-118">Le type de paramètre doit être Sérialisable JSON, ce qui signifie généralement que le type doit avoir un constructeur par défaut et des propriétés settable.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="dc8d1-119">Par exemple, vous pouvez `Size` spécifier une valeur pour `Color` et dans l’exemple précédent parce que les types `Size` et `Color` sont des types primitifs (`int` et `string`), qui sont pris en charge par le sérialisateur JSON.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="dc8d1-120">Dans l’exemple suivant, un objet de classe est transmis au composant :</span><span class="sxs-lookup"><span data-stu-id="dc8d1-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="dc8d1-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="dc8d1-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="dc8d1-122">**La classe doit avoir un constructeur public sans paramètres.**</span><span class="sxs-lookup"><span data-stu-id="dc8d1-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="dc8d1-123">*Partagé/MyComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="dc8d1-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="dc8d1-124">*Pages/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="dc8d1-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="dc8d1-125">L’exemple précédent suppose `MyComponent` que le composant se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="dc8d1-126">`MyClass`est dans l’espace nom`{APP ASSEMBLY}`de l’application ( ).</span><span class="sxs-lookup"><span data-stu-id="dc8d1-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="dc8d1-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="dc8d1-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="dc8d1-128">Est préditulé dans la page.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="dc8d1-129">Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="dc8d1-130">Mode Rendu</span><span class="sxs-lookup"><span data-stu-id="dc8d1-130">Render Mode</span></span> | <span data-ttu-id="dc8d1-131">Description</span><span class="sxs-lookup"><span data-stu-id="dc8d1-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="dc8d1-132">Rend le composant en HTML statique et Blazor inclut un marqueur pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="dc8d1-133">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="dc8d1-134">Rend un marqueur Blazor pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="dc8d1-135">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-135">Output from the component isn't included.</span></span> <span data-ttu-id="dc8d1-136">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="dc8d1-137">Rend le composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="dc8d1-138">Bien que les pages et les vues puissent utiliser des composants, l’inverse n’est pas vrai.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="dc8d1-139">Les composants ne peuvent pas utiliser des fonctionnalités spécifiques à la vue et à la page, telles que des vues partielles et des sections.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="dc8d1-140">Pour utiliser la logique d’une vue partielle dans un composant, tenez compte de la logique de vue partielle dans un composant.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="dc8d1-141">Le rendu des composants du serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="dc8d1-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc8d1-142">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="dc8d1-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
