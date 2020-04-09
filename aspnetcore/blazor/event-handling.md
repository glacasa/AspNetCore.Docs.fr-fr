---
title: ASP.NET la gestion Blazor d’événements de base
author: guardrex
description: Renseignez-vous sur Blazorles fonctionnalités de traitement des événements, y compris les types d’arguments d’événements, les rappels d’événements et la gestion des événements par défaut du navigateur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511364"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET gestion de l’événement Core Blazor

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Les composants Razor fournissent des caractéristiques de manipulation d’événements. Pour un attribut [`@on{EVENT}`](xref:mvc/views/razor#onevent) d’élément `@onclick`HTML nommé (par exemple) avec une valeur dactylographe, un composant Razor traite la valeur de l’attribut en tant que gestionnaire d’événements.

Le code suivant `UpdateHeading` appelle la méthode lorsque le bouton est sélectionné dans l’interface utilisateur :

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Le code suivant `CheckChanged` appelle la méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Les gestionnaires d’événements peuvent également être <xref:System.Threading.Tasks.Task>asynchrones et retourner un . Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/lifecycle#state-changes). Les exceptions sont enregistrées lorsqu’elles se produisent.

Dans l’exemple `UpdateHeading` suivant, est appelé asynchrone lorsque le bouton est sélectionné:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a>Types d’argument d’événement

Pour certains événements, les types d’arguments d’événements sont autorisés. La spécifier un type d’événement dans l’appel de méthode n’est nécessaire que si le type d’événement est utilisé dans la méthode.

Les `EventArgs` prises en charge sont indiquées dans le tableau suivant.

| Événement            | Classe                | Événements et notes DOM |
| ---------------- | -------------------- | -------------------- |
| Presse-papiers        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Glissement             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`et `DataTransferItem` tenir les données de l’élément traîné. |
| Error            | `ErrorEventArgs`     | `onerror` |
| Événement            | `EventArgs`          | *Généralités*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Presse-papiers*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Médias*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>N’inclut pas `relatedTarget`le soutien pour . |
| Entrée            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Clavier         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Souris            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Pointeur de souris    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Roulette de la souris      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Progress         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Entrées tactiles            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`représente un point de contact unique sur un appareil tactile. |

Pour plus d’informations, consultez les ressources suivantes :

* [EventArgs classes dans la source de référence ASP.NET Core (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [DOCs Web MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Inclut des informations sur les éléments HTML qui prennent en charge chaque événement DOM.

## <a name="lambda-expressions"></a>Expressions lambda

[Les expressions Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Il est souvent pratique de fermer sur des valeurs supplémentaires, comme lors de l’itération sur un ensemble d’éléments. L’exemple suivant crée trois boutons, `UpdateHeading` chacun d’entre eux passant un argument d’événement ()`MouseEventArgs`et son numéro de bouton (`buttonNumber`) lorsqu’il est sélectionné dans l’interface utilisateur:

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> N’utilisez **pas** la`i`variable `for` de boucle ( ) dans une boucle directement dans une expression lambda. Sinon, la même variable est utilisée `i`par toutes les expressions lambda causant la valeur de «être le même dans tous les lambdas. Toujours saisir sa valeur dans`buttonNumber` une variable locale (dans l’exemple précédent) et ensuite l’utiliser.

## <a name="eventcallback"></a>EventCallback

Un scénario commun avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de composante enfant se produit&mdash;par exemple, lorsqu’un `onclick` événement se produit chez l’enfant. Pour exposer les événements à `EventCallback`travers les composants, utilisez un . Un composant parent peut attribuer une méthode de `EventCallback`rappel à un composant d’enfant.

L’application `ChildComponent` de l’échantillon *(Composants/ChildComponent.razor*) montre `onclick` comment le gestionnaire `EventCallback` d’un bouton `ParentComponent`est configuré pour recevoir un délégué de l’échantillon. Le `EventCallback` est tapé avec `MouseEventArgs`, `onclick` qui est approprié pour un événement à partir d’un périphérique:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Le `ParentComponent` définit `EventCallback<T>` l’enfant`OnClickCallback`( `ShowMessage` ) à sa méthode.

*Pages/ParentComponent.razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Lorsque le bouton est `ChildComponent`sélectionné dans le :

* La `ParentComponent`méthode `ShowMessage` est appelée. `_messageText`est mis à `ParentComponent`jour et affiché dans le .
* Un appel à [StateHasChanged](xref:blazor/lifecycle#state-changes) n’est pas nécessaire`ShowMessage`dans la méthode du rappel ( ). `StateHasChanged`est appelé automatiquement pour `ParentComponent`rerender le , tout comme les événements de l’enfant déclenchent le rerendering composant dans les gestionnaires d’événements qui exécutent au sein de l’enfant.

`EventCallback`et `EventCallback<T>` permettre aux délégués asynchrones. `EventCallback<T>`est fortement tapé et nécessite un type d’argument spécifique. `EventCallback`est faiblement tapé et permet n’importe quel type d’argument.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Invoquez un `EventCallback` ou `EventCallback<T>` avec `InvokeAsync` et attendez le <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Utilisation `EventCallback` `EventCallback<T>` et pour la gestion des événements et les paramètres de composants contraignants.

Préférez le fortement `EventCallback<T>` `EventCallback`tapé sur . `EventCallback<T>`fournit une meilleure rétroaction d’erreur aux utilisateurs du composant. Semblable à d’autres gestionnaires d’événements d’interface utilisateur, la spécifier le paramètre de l’événement est facultative. Utilisez `EventCallback` quand il n’y a aucune valeur passée à la rappel.

## <a name="prevent-default-actions"></a>Prévenir les actions par défaut

Utilisez [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) l’attribut de directive pour empêcher l’action par défaut pour un événement.

Lorsqu’une clé est sélectionnée sur un périphérique d’entrée et que l’élément se concentre sur une boîte de texte, un navigateur affiche normalement le caractère de la clé dans la boîte de texte. Dans l’exemple suivant, le comportement par `@onkeypress:preventDefault` défaut est évité en spécifiant l’attribut de directive. Les incréments de **+** compteur, et la `<input>` clé n’est pas capturé dans la valeur de l’élément:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

Spécifier l’attribut `@on{EVENT}:preventDefault` sans `@on{EVENT}:preventDefault="true"`valeur équivaut à .

La valeur de l’attribut peut également être une expression. Dans l’exemple `_shouldPreventDefault` suivant, est un `bool` champ mis à l’un ou l’autre `true` ou `false`:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Un gestionnaire d’événements n’est pas tenu d’empêcher l’action par défaut. Le gestionnaire d’événements et de prévenir les scénarios d’action par défaut peuvent être utilisés de façon indépendante.

## <a name="stop-event-propagation"></a>Arrêter la propagation de l’événement

Utilisez [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) l’attribut de la directive pour arrêter la propagation de l’événement.

Dans l’exemple suivant, la sélection de la case `<div>` à cocher empêche `<div>`les événements de clic du deuxième enfant de se propager au parent :

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
