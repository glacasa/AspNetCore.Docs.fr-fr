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
# <a name="component-tag-helper-in-aspnet-core"></a>Aide à l’étiquette de composant dans ASP.NET noyau

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Pour rendre un composant à partir d’une page ou d’une vue, utilisez [l’aide à l’étiquette de composant](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Prérequis

Suivez les conseils de *l’application Préparer à utiliser* des <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> composants dans les pages et les vues de l’article.

## <a name="component-tag-helper"></a>Aide à l’étiquette de composant

L’aide à l’étiquette `Counter` de composant suivante rend le composant dans une page ou une vue :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

L’exemple précédent suppose `Counter` que le composant se trouve dans le dossier *Pages* de l’application.

L’aide à l’étiquette de composant peut également passer des paramètres aux composants. Considérez `ColorfulCheckbox` le composant suivant qui définit la couleur et la taille de l’étiquette de la case à cocher :

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

Les `Size` `int`paramètres `Color` des`string`composants ( ) et ) peuvent être [définis](xref:blazor/components#component-parameters) par l’aide à l’étiquette des composants :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

L’exemple précédent suppose `ColorfulCheckbox` que le composant se trouve dans le dossier *partagé* de l’application.

Le HTML suivant est rendu dans la page ou la vue:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Passer une chaîne citée nécessite une expression `param-Color` explicite [Razor](xref:mvc/views/razor#explicit-razor-expressions), comme indiqué dans l’exemple précédent. Le comportement d’analyse `string` Razor pour une valeur `param-*` de type ne `object` s’applique pas à un attribut parce que l’attribut est un type.

Le type de paramètre doit être Sérialisable JSON, ce qui signifie généralement que le type doit avoir un constructeur par défaut et des propriétés settable. Par exemple, vous pouvez `Size` spécifier une valeur pour `Color` et dans l’exemple précédent parce que les types `Size` et `Color` sont des types primitifs (`int` et `string`), qui sont pris en charge par le sérialisateur JSON.

Dans l’exemple suivant, un objet de classe est transmis au composant :

*MyClass.cs*:

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

**La classe doit avoir un constructeur public sans paramètres.**

*Partagé/MyComponent.razor*:

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

*Pages/MyPage.cshtml*:

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

L’exemple précédent suppose `MyComponent` que le composant se trouve dans le dossier *partagé* de l’application. `MyClass`est dans l’espace nom`{APP ASSEMBLY}`de l’application ( ).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configure si le composant :

* Est préditulé dans la page.
* Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.

| Mode Rendu | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rend le composant en HTML statique et Blazor inclut un marqueur pour une application Server. Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rend un marqueur Blazor pour une application Server. La sortie du composant n’est pas incluse. Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rend le composant en HTML statique. |

Bien que les pages et les vues puissent utiliser des composants, l’inverse n’est pas vrai. Les composants ne peuvent pas utiliser des fonctionnalités spécifiques à la vue et à la page, telles que des vues partielles et des sections. Pour utiliser la logique d’une vue partielle dans un composant, tenez compte de la logique de vue partielle dans un composant.

Le rendu des composants du serveur à partir d’une page HTML statique n’est pas pris en charge.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
