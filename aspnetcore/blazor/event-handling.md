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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="9d3ab-103">ASP.NET gestion de l’événement Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9d3ab-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="9d3ab-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9d3ab-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9d3ab-105">Les composants Razor fournissent des caractéristiques de manipulation d’événements.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-105">Razor components provide event handling features.</span></span> <span data-ttu-id="9d3ab-106">Pour un attribut [`@on{EVENT}`](xref:mvc/views/razor#onevent) d’élément `@onclick`HTML nommé (par exemple) avec une valeur dactylographe, un composant Razor traite la valeur de l’attribut en tant que gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="9d3ab-107">Le code suivant `UpdateHeading` appelle la méthode lorsque le bouton est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="9d3ab-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="9d3ab-108">Le code suivant `CheckChanged` appelle la méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="9d3ab-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="9d3ab-109">Les gestionnaires d’événements peuvent également être <xref:System.Threading.Tasks.Task>asynchrones et retourner un .</span><span class="sxs-lookup"><span data-stu-id="9d3ab-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="9d3ab-110">Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="9d3ab-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="9d3ab-111">Les exceptions sont enregistrées lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="9d3ab-112">Dans l’exemple `UpdateHeading` suivant, est appelé asynchrone lorsque le bouton est sélectionné:</span><span class="sxs-lookup"><span data-stu-id="9d3ab-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="9d3ab-113">Types d’argument d’événement</span><span class="sxs-lookup"><span data-stu-id="9d3ab-113">Event argument types</span></span>

<span data-ttu-id="9d3ab-114">Pour certains événements, les types d’arguments d’événements sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="9d3ab-115">La spécifier un type d’événement dans l’appel de méthode n’est nécessaire que si le type d’événement est utilisé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="9d3ab-116">Les `EventArgs` prises en charge sont indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="9d3ab-117">Événement</span><span class="sxs-lookup"><span data-stu-id="9d3ab-117">Event</span></span>            | <span data-ttu-id="9d3ab-118">Classe</span><span class="sxs-lookup"><span data-stu-id="9d3ab-118">Class</span></span>                | <span data-ttu-id="9d3ab-119">Événements et notes DOM</span><span class="sxs-lookup"><span data-stu-id="9d3ab-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="9d3ab-120">Presse-papiers</span><span class="sxs-lookup"><span data-stu-id="9d3ab-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="9d3ab-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="9d3ab-122">Glissement</span><span class="sxs-lookup"><span data-stu-id="9d3ab-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="9d3ab-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="9d3ab-124">`DataTransfer`et `DataTransferItem` tenir les données de l’élément traîné.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="9d3ab-125">Error</span><span class="sxs-lookup"><span data-stu-id="9d3ab-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="9d3ab-126">Événement</span><span class="sxs-lookup"><span data-stu-id="9d3ab-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="9d3ab-127">*Généralités*</span><span class="sxs-lookup"><span data-stu-id="9d3ab-127">*General*</span></span><br><span data-ttu-id="9d3ab-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="9d3ab-129">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="9d3ab-129">*Clipboard*</span></span><br><span data-ttu-id="9d3ab-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="9d3ab-131">*Input*</span><span class="sxs-lookup"><span data-stu-id="9d3ab-131">*Input*</span></span><br><span data-ttu-id="9d3ab-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="9d3ab-133">*Médias*</span><span class="sxs-lookup"><span data-stu-id="9d3ab-133">*Media*</span></span><br><span data-ttu-id="9d3ab-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="9d3ab-135">Focus</span><span class="sxs-lookup"><span data-stu-id="9d3ab-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="9d3ab-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="9d3ab-137">N’inclut pas `relatedTarget`le soutien pour .</span><span class="sxs-lookup"><span data-stu-id="9d3ab-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="9d3ab-138">Entrée</span><span class="sxs-lookup"><span data-stu-id="9d3ab-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="9d3ab-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="9d3ab-140">Clavier</span><span class="sxs-lookup"><span data-stu-id="9d3ab-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="9d3ab-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="9d3ab-142">Souris</span><span class="sxs-lookup"><span data-stu-id="9d3ab-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="9d3ab-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="9d3ab-144">Pointeur de souris</span><span class="sxs-lookup"><span data-stu-id="9d3ab-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="9d3ab-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="9d3ab-146">Roulette de la souris</span><span class="sxs-lookup"><span data-stu-id="9d3ab-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="9d3ab-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="9d3ab-148">Progress</span><span class="sxs-lookup"><span data-stu-id="9d3ab-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="9d3ab-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="9d3ab-150">Entrées tactiles</span><span class="sxs-lookup"><span data-stu-id="9d3ab-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="9d3ab-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="9d3ab-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="9d3ab-152">`TouchPoint`représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="9d3ab-153">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="9d3ab-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="9d3ab-154">[EventArgs classes dans la source de référence ASP.NET Core (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="9d3ab-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="9d3ab-155">[DOCs Web MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Inclut des informations sur les éléments HTML qui prennent en charge chaque événement DOM.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="9d3ab-156">Expressions lambda</span><span class="sxs-lookup"><span data-stu-id="9d3ab-156">Lambda expressions</span></span>

<span data-ttu-id="9d3ab-157">[Les expressions Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :</span><span class="sxs-lookup"><span data-stu-id="9d3ab-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="9d3ab-158">Il est souvent pratique de fermer sur des valeurs supplémentaires, comme lors de l’itération sur un ensemble d’éléments.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="9d3ab-159">L’exemple suivant crée trois boutons, `UpdateHeading` chacun d’entre eux passant un argument d’événement ()`MouseEventArgs`et son numéro de bouton (`buttonNumber`) lorsqu’il est sélectionné dans l’interface utilisateur:</span><span class="sxs-lookup"><span data-stu-id="9d3ab-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="9d3ab-160">N’utilisez **pas** la`i`variable `for` de boucle ( ) dans une boucle directement dans une expression lambda.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="9d3ab-161">Sinon, la même variable est utilisée `i`par toutes les expressions lambda causant la valeur de «être le même dans tous les lambdas.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="9d3ab-162">Toujours saisir sa valeur dans`buttonNumber` une variable locale (dans l’exemple précédent) et ensuite l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="9d3ab-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="9d3ab-163">EventCallback</span></span>

<span data-ttu-id="9d3ab-164">Un scénario commun avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de composante enfant se produit&mdash;par exemple, lorsqu’un `onclick` événement se produit chez l’enfant.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="9d3ab-165">Pour exposer les événements à `EventCallback`travers les composants, utilisez un .</span><span class="sxs-lookup"><span data-stu-id="9d3ab-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="9d3ab-166">Un composant parent peut attribuer une méthode de `EventCallback`rappel à un composant d’enfant.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="9d3ab-167">L’application `ChildComponent` de l’échantillon *(Composants/ChildComponent.razor*) montre `onclick` comment le gestionnaire `EventCallback` d’un bouton `ParentComponent`est configuré pour recevoir un délégué de l’échantillon.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="9d3ab-168">Le `EventCallback` est tapé avec `MouseEventArgs`, `onclick` qui est approprié pour un événement à partir d’un périphérique:</span><span class="sxs-lookup"><span data-stu-id="9d3ab-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="9d3ab-169">Le `ParentComponent` définit `EventCallback<T>` l’enfant`OnClickCallback`( `ShowMessage` ) à sa méthode.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="9d3ab-170">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="9d3ab-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="9d3ab-171">Lorsque le bouton est `ChildComponent`sélectionné dans le :</span><span class="sxs-lookup"><span data-stu-id="9d3ab-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="9d3ab-172">La `ParentComponent`méthode `ShowMessage` est appelée.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="9d3ab-173">`_messageText`est mis à `ParentComponent`jour et affiché dans le .</span><span class="sxs-lookup"><span data-stu-id="9d3ab-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="9d3ab-174">Un appel à [StateHasChanged](xref:blazor/lifecycle#state-changes) n’est pas nécessaire`ShowMessage`dans la méthode du rappel ( ).</span><span class="sxs-lookup"><span data-stu-id="9d3ab-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="9d3ab-175">`StateHasChanged`est appelé automatiquement pour `ParentComponent`rerender le , tout comme les événements de l’enfant déclenchent le rerendering composant dans les gestionnaires d’événements qui exécutent au sein de l’enfant.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="9d3ab-176">`EventCallback`et `EventCallback<T>` permettre aux délégués asynchrones.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="9d3ab-177">`EventCallback<T>`est fortement tapé et nécessite un type d’argument spécifique.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="9d3ab-178">`EventCallback`est faiblement tapé et permet n’importe quel type d’argument.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="9d3ab-179">Invoquez un `EventCallback` ou `EventCallback<T>` avec `InvokeAsync` et attendez le <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="9d3ab-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="9d3ab-180">Utilisation `EventCallback` `EventCallback<T>` et pour la gestion des événements et les paramètres de composants contraignants.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="9d3ab-181">Préférez le fortement `EventCallback<T>` `EventCallback`tapé sur .</span><span class="sxs-lookup"><span data-stu-id="9d3ab-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="9d3ab-182">`EventCallback<T>`fournit une meilleure rétroaction d’erreur aux utilisateurs du composant.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="9d3ab-183">Semblable à d’autres gestionnaires d’événements d’interface utilisateur, la spécifier le paramètre de l’événement est facultative.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="9d3ab-184">Utilisez `EventCallback` quand il n’y a aucune valeur passée à la rappel.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="9d3ab-185">Prévenir les actions par défaut</span><span class="sxs-lookup"><span data-stu-id="9d3ab-185">Prevent default actions</span></span>

<span data-ttu-id="9d3ab-186">Utilisez [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) l’attribut de directive pour empêcher l’action par défaut pour un événement.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="9d3ab-187">Lorsqu’une clé est sélectionnée sur un périphérique d’entrée et que l’élément se concentre sur une boîte de texte, un navigateur affiche normalement le caractère de la clé dans la boîte de texte.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="9d3ab-188">Dans l’exemple suivant, le comportement par `@onkeypress:preventDefault` défaut est évité en spécifiant l’attribut de directive.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="9d3ab-189">Les incréments de **+** compteur, et la `<input>` clé n’est pas capturé dans la valeur de l’élément:</span><span class="sxs-lookup"><span data-stu-id="9d3ab-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="9d3ab-190">Spécifier l’attribut `@on{EVENT}:preventDefault` sans `@on{EVENT}:preventDefault="true"`valeur équivaut à .</span><span class="sxs-lookup"><span data-stu-id="9d3ab-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="9d3ab-191">La valeur de l’attribut peut également être une expression.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="9d3ab-192">Dans l’exemple `_shouldPreventDefault` suivant, est un `bool` champ mis à l’un ou l’autre `true` ou `false`:</span><span class="sxs-lookup"><span data-stu-id="9d3ab-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="9d3ab-193">Un gestionnaire d’événements n’est pas tenu d’empêcher l’action par défaut.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="9d3ab-194">Le gestionnaire d’événements et de prévenir les scénarios d’action par défaut peuvent être utilisés de façon indépendante.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="9d3ab-195">Arrêter la propagation de l’événement</span><span class="sxs-lookup"><span data-stu-id="9d3ab-195">Stop event propagation</span></span>

<span data-ttu-id="9d3ab-196">Utilisez [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) l’attribut de la directive pour arrêter la propagation de l’événement.</span><span class="sxs-lookup"><span data-stu-id="9d3ab-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="9d3ab-197">Dans l’exemple suivant, la sélection de la case `<div>` à cocher empêche `<div>`les événements de clic du deuxième enfant de se propager au parent :</span><span class="sxs-lookup"><span data-stu-id="9d3ab-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
