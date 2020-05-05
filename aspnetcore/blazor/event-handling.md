---
title: Gestion Blazor des événements ASP.net Core
author: guardrex
description: Découvrez les Blazorfonctionnalités de gestion des événements de, notamment les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: a9b0d0efd4afd4941bd4d93f33adecdf3288992f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767068"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="d69f1-103">ASP.NET Core la gestion des événements éblouissants</span><span class="sxs-lookup"><span data-stu-id="d69f1-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="d69f1-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d69f1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d69f1-105">Les composants Razor fournissent des fonctionnalités de gestion des événements.</span><span class="sxs-lookup"><span data-stu-id="d69f1-105">Razor components provide event handling features.</span></span> <span data-ttu-id="d69f1-106">Pour un attribut d’élément HTML [`@on{EVENT}`](xref:mvc/views/razor#onevent) nommé (par exemple `@onclick`,) avec une valeur de type délégué, un composant Razor traite la valeur de l’attribut en tant que gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="d69f1-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="d69f1-107">Le code suivant appelle la `UpdateHeading` méthode lorsque le bouton est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="d69f1-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="d69f1-108">Le code suivant appelle la `CheckChanged` méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="d69f1-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="d69f1-109">Les gestionnaires d’événements peuvent également être asynchrones et <xref:System.Threading.Tasks.Task>retourner un.</span><span class="sxs-lookup"><span data-stu-id="d69f1-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="d69f1-110">Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="d69f1-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="d69f1-111">Les exceptions sont journalisées lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="d69f1-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="d69f1-112">Dans l’exemple suivant, `UpdateHeading` est appelé de façon asynchrone quand le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="d69f1-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="d69f1-113">Types d’arguments d’événement</span><span class="sxs-lookup"><span data-stu-id="d69f1-113">Event argument types</span></span>

<span data-ttu-id="d69f1-114">Pour certains événements, les types d’arguments d’événement sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="d69f1-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="d69f1-115">La spécification d’un type d’événement dans l’appel de méthode n’est nécessaire que si le type d’événement est utilisé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="d69f1-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="d69f1-116">Les `EventArgs` informations prises en charge sont indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="d69f1-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="d69f1-117">Événement</span><span class="sxs-lookup"><span data-stu-id="d69f1-117">Event</span></span>            | <span data-ttu-id="d69f1-118">Classe</span><span class="sxs-lookup"><span data-stu-id="d69f1-118">Class</span></span>                | <span data-ttu-id="d69f1-119">Remarques et événements DOM</span><span class="sxs-lookup"><span data-stu-id="d69f1-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="d69f1-120">Presse-papiers</span><span class="sxs-lookup"><span data-stu-id="d69f1-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="d69f1-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="d69f1-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="d69f1-122">Glissement</span><span class="sxs-lookup"><span data-stu-id="d69f1-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="d69f1-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="d69f1-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="d69f1-124">`DataTransfer`et `DataTransferItem` contiennent des données d’élément glissées.</span><span class="sxs-lookup"><span data-stu-id="d69f1-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="d69f1-125">Error</span><span class="sxs-lookup"><span data-stu-id="d69f1-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="d69f1-126">Événement</span><span class="sxs-lookup"><span data-stu-id="d69f1-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="d69f1-127">*Généralités*</span><span class="sxs-lookup"><span data-stu-id="d69f1-127">*General*</span></span><br><span data-ttu-id="d69f1-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="d69f1-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="d69f1-129">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="d69f1-129">*Clipboard*</span></span><br><span data-ttu-id="d69f1-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="d69f1-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="d69f1-131">*Input*</span><span class="sxs-lookup"><span data-stu-id="d69f1-131">*Input*</span></span><br><span data-ttu-id="d69f1-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="d69f1-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="d69f1-133">*Média*</span><span class="sxs-lookup"><span data-stu-id="d69f1-133">*Media*</span></span><br><span data-ttu-id="d69f1-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="d69f1-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="d69f1-135">Focus</span><span class="sxs-lookup"><span data-stu-id="d69f1-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="d69f1-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="d69f1-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="d69f1-137">N’inclut pas la `relatedTarget`prise en charge de.</span><span class="sxs-lookup"><span data-stu-id="d69f1-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="d69f1-138">Entrée</span><span class="sxs-lookup"><span data-stu-id="d69f1-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="d69f1-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="d69f1-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="d69f1-140">Clavier</span><span class="sxs-lookup"><span data-stu-id="d69f1-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="d69f1-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="d69f1-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="d69f1-142">Souris</span><span class="sxs-lookup"><span data-stu-id="d69f1-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="d69f1-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="d69f1-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="d69f1-144">Pointeur de souris</span><span class="sxs-lookup"><span data-stu-id="d69f1-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="d69f1-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="d69f1-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="d69f1-146">Roulette de la souris</span><span class="sxs-lookup"><span data-stu-id="d69f1-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="d69f1-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="d69f1-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="d69f1-148">Progress</span><span class="sxs-lookup"><span data-stu-id="d69f1-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="d69f1-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="d69f1-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="d69f1-150">Toucher</span><span class="sxs-lookup"><span data-stu-id="d69f1-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="d69f1-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="d69f1-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="d69f1-152">`TouchPoint`représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="d69f1-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="d69f1-153">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="d69f1-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="d69f1-154">[Classes EventArgs dans la source de référence ASP.net Core (branche dotnet/aspnetcore ou version 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="d69f1-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="d69f1-155">[MDN Web docs : GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; contient des informations sur les éléments HTML qui prennent en charge chaque événement DOM.</span><span class="sxs-lookup"><span data-stu-id="d69f1-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="d69f1-156">Expressions lambda</span><span class="sxs-lookup"><span data-stu-id="d69f1-156">Lambda expressions</span></span>

<span data-ttu-id="d69f1-157">Les [expressions lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :</span><span class="sxs-lookup"><span data-stu-id="d69f1-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="d69f1-158">Il est souvent pratique de se rapprocher de valeurs supplémentaires, par exemple lors de l’itération sur un ensemble d’éléments.</span><span class="sxs-lookup"><span data-stu-id="d69f1-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="d69f1-159">L’exemple suivant crée trois boutons, chacun d’entre eux `UpdateHeading` qui passe un argument d'`MouseEventArgs`événement () et son numéro`buttonNumber`de bouton () lorsqu’ils sont sélectionnés dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="d69f1-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="d69f1-160">N’utilisez **pas** la variable de boucle`i`() dans `for` une boucle directement dans une expression lambda.</span><span class="sxs-lookup"><span data-stu-id="d69f1-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="d69f1-161">Dans le cas contraire, la même variable est utilisée par `i`toutes les expressions lambda, ce qui signifie que la valeur de est identique dans toutes les expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="d69f1-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="d69f1-162">Capturez toujours sa valeur dans une variable locale`buttonNumber` (dans l’exemple précédent), puis utilisez-la.</span><span class="sxs-lookup"><span data-stu-id="d69f1-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="d69f1-163">EventCallback suivante</span><span class="sxs-lookup"><span data-stu-id="d69f1-163">EventCallback</span></span>

<span data-ttu-id="d69f1-164">Un scénario courant avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de&mdash;composant enfant se produit, `onclick` par exemple, lorsqu’un événement se produit dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="d69f1-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="d69f1-165">Pour exposer des événements entre les composants, `EventCallback`utilisez un.</span><span class="sxs-lookup"><span data-stu-id="d69f1-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="d69f1-166">Un composant parent peut affecter une méthode de rappel à un composant enfant `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="d69f1-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="d69f1-167">`ChildComponent` Dans l’exemple d’application (*Components/ChildComponent. Razor*), vous montre comment `onclick` un gestionnaire de bouton est configuré pour `EventCallback` recevoir un délégué de l' `ParentComponent`exemple de.</span><span class="sxs-lookup"><span data-stu-id="d69f1-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="d69f1-168">Le `EventCallback` est typé avec `MouseEventArgs`, ce qui est approprié pour `onclick` un événement à partir d’un périphérique :</span><span class="sxs-lookup"><span data-stu-id="d69f1-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="d69f1-169">`ParentComponent` Définit le (`OnClickCallback`) de `EventCallback<T>` l’enfant sur sa `ShowMessage` méthode.</span><span class="sxs-lookup"><span data-stu-id="d69f1-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="d69f1-170">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d69f1-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="d69f1-171">Lorsque le bouton est sélectionné dans le `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="d69f1-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="d69f1-172">La `ParentComponent`méthode `ShowMessage` de est appelée.</span><span class="sxs-lookup"><span data-stu-id="d69f1-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="d69f1-173">`_messageText`est mis à jour et affiché `ParentComponent`dans le.</span><span class="sxs-lookup"><span data-stu-id="d69f1-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="d69f1-174">Un appel à [StateHasChanged](xref:blazor/lifecycle#state-changes) n’est pas requis dans la méthode du`ShowMessage`rappel ().</span><span class="sxs-lookup"><span data-stu-id="d69f1-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="d69f1-175">`StateHasChanged`est appelé automatiquement pour rerestituer `ParentComponent`le, tout comme les événements enfants déclenchent le rerendu des composants dans les gestionnaires d’événements qui s’exécutent dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="d69f1-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="d69f1-176">`EventCallback`et `EventCallback<T>` autorisent les délégués asynchrones.</span><span class="sxs-lookup"><span data-stu-id="d69f1-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="d69f1-177">`EventCallback<T>`est fortement typé et requiert un type d’argument spécifique.</span><span class="sxs-lookup"><span data-stu-id="d69f1-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="d69f1-178">`EventCallback`est faiblement typé et autorise tout type d’argument.</span><span class="sxs-lookup"><span data-stu-id="d69f1-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="d69f1-179">Appelez `EventCallback` ou `EventCallback<T>` avec et `InvokeAsync` en attente de <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="d69f1-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="d69f1-180">Utilisez `EventCallback` et `EventCallback<T>` pour la gestion des événements et les paramètres de composant de liaison.</span><span class="sxs-lookup"><span data-stu-id="d69f1-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="d69f1-181">Préférez le fortement typé `EventCallback<T>` `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="d69f1-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="d69f1-182">`EventCallback<T>`fournit un meilleur retour d’erreur aux utilisateurs du composant.</span><span class="sxs-lookup"><span data-stu-id="d69f1-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="d69f1-183">Comme pour d’autres gestionnaires d’événements d’interface utilisateur, la spécification du paramètre d’événement est facultative.</span><span class="sxs-lookup"><span data-stu-id="d69f1-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="d69f1-184">Utilisez `EventCallback` quand aucune valeur n’est passée au rappel.</span><span class="sxs-lookup"><span data-stu-id="d69f1-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="d69f1-185">Empêcher les actions par défaut</span><span class="sxs-lookup"><span data-stu-id="d69f1-185">Prevent default actions</span></span>

<span data-ttu-id="d69f1-186">Utilisez l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attribut directive pour empêcher l’action par défaut d’un événement.</span><span class="sxs-lookup"><span data-stu-id="d69f1-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="d69f1-187">Quand une clé est sélectionnée sur un appareil d’entrée et que le focus de l’élément se trouve sur une zone de texte, un navigateur affiche normalement le caractère de la clé dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="d69f1-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="d69f1-188">Dans l’exemple suivant, le comportement par défaut est évité en spécifiant l' `@onkeypress:preventDefault` attribut directive.</span><span class="sxs-lookup"><span data-stu-id="d69f1-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="d69f1-189">Le compteur est incrémenté et la **+** clé n’est pas capturée dans la `<input>` valeur de l’élément :</span><span class="sxs-lookup"><span data-stu-id="d69f1-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="d69f1-190">La spécification `@on{EVENT}:preventDefault` de l’attribut sans valeur équivaut à `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="d69f1-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="d69f1-191">La valeur de l’attribut peut également être une expression.</span><span class="sxs-lookup"><span data-stu-id="d69f1-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="d69f1-192">Dans l’exemple suivant, `_shouldPreventDefault` est un `bool` champ défini sur `true` ou `false`:</span><span class="sxs-lookup"><span data-stu-id="d69f1-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="d69f1-193">Un gestionnaire d’événements n’est pas nécessaire pour empêcher l’action par défaut.</span><span class="sxs-lookup"><span data-stu-id="d69f1-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="d69f1-194">Le gestionnaire d’événements et les scénarios d’action par défaut peuvent être utilisés indépendamment.</span><span class="sxs-lookup"><span data-stu-id="d69f1-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="d69f1-195">Arrêter la propagation des événements</span><span class="sxs-lookup"><span data-stu-id="d69f1-195">Stop event propagation</span></span>

<span data-ttu-id="d69f1-196">Utilisez l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attribut directive pour arrêter la propagation des événements.</span><span class="sxs-lookup"><span data-stu-id="d69f1-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="d69f1-197">Dans l’exemple suivant, la sélection de la case à cocher empêche les événements `<div>` Click du deuxième enfant de se `<div>`propager vers le parent :</span><span class="sxs-lookup"><span data-stu-id="d69f1-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
