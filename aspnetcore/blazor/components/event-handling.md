---
title: BlazorGestion des événements ASP.net Core
author: guardrex
description: Découvrez Blazor les fonctionnalités de gestion des événements de, notamment les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 32f7595cffc2c31116c8d876c9f9526b84c52f14
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103725"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="559ee-103">BlazorGestion des événements ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="559ee-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="559ee-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="559ee-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="559ee-105">les composants fournissent des fonctionnalités de gestion des événements.</span><span class="sxs-lookup"><span data-stu-id="559ee-105"> components provide event handling features.</span></span> <span data-ttu-id="559ee-106">Pour un attribut d’élément HTML nommé [`@on{EVENT}`](xref:mvc/views/razor#onevent) (par exemple, `@onclick` ) avec une valeur de type délégué, un Razor composant traite la valeur de l’attribut en tant que gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="559ee-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="559ee-107">Le code suivant appelle la `UpdateHeading` méthode lorsque le bouton est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="559ee-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="559ee-108">Le code suivant appelle la `CheckChanged` méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="559ee-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="559ee-109">Les gestionnaires d’événements peuvent également être asynchrones et retourner un <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="559ee-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="559ee-110">Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="559ee-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="559ee-111">Les exceptions sont journalisées lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="559ee-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="559ee-112">Dans l’exemple suivant, `UpdateHeading` est appelé de façon asynchrone quand le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="559ee-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="559ee-113">Types d’arguments d’événement</span><span class="sxs-lookup"><span data-stu-id="559ee-113">Event argument types</span></span>

<span data-ttu-id="559ee-114">Pour certains événements, les types d’arguments d’événement sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="559ee-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="559ee-115">La spécification d’un type d’événement dans l’appel de méthode n’est nécessaire que si le type d’événement est utilisé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="559ee-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="559ee-116">Les informations prises en charge <xref:System.EventArgs> sont indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="559ee-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="559ee-117">Événement</span><span class="sxs-lookup"><span data-stu-id="559ee-117">Event</span></span>            | <span data-ttu-id="559ee-118">Classe</span><span class="sxs-lookup"><span data-stu-id="559ee-118">Class</span></span>                | <span data-ttu-id="559ee-119">Remarques et événements DOM</span><span class="sxs-lookup"><span data-stu-id="559ee-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="559ee-120">Presse-papiers</span><span class="sxs-lookup"><span data-stu-id="559ee-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="559ee-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="559ee-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="559ee-122">Glissement</span><span class="sxs-lookup"><span data-stu-id="559ee-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="559ee-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="559ee-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="559ee-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>et <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contiennent des données d’élément glissées.</span><span class="sxs-lookup"><span data-stu-id="559ee-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="559ee-125">Erreur</span><span class="sxs-lookup"><span data-stu-id="559ee-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="559ee-126">Événement</span><span class="sxs-lookup"><span data-stu-id="559ee-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="559ee-127">*Généralités*</span><span class="sxs-lookup"><span data-stu-id="559ee-127">*General*</span></span><br><span data-ttu-id="559ee-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="559ee-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="559ee-129">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="559ee-129">*Clipboard*</span></span><br><span data-ttu-id="559ee-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="559ee-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="559ee-131">*Input*</span><span class="sxs-lookup"><span data-stu-id="559ee-131">*Input*</span></span><br><span data-ttu-id="559ee-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="559ee-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="559ee-133">*Média*</span><span class="sxs-lookup"><span data-stu-id="559ee-133">*Media*</span></span><br><span data-ttu-id="559ee-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="559ee-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="559ee-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>contient des attributs permettant de configurer les mappages entre les noms d’événements et les types d’arguments d’événement.</span><span class="sxs-lookup"><span data-stu-id="559ee-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="559ee-136">Focus</span><span class="sxs-lookup"><span data-stu-id="559ee-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="559ee-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="559ee-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="559ee-138">N’inclut pas la prise en charge de `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="559ee-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="559ee-139">Entrée</span><span class="sxs-lookup"><span data-stu-id="559ee-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="559ee-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="559ee-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="559ee-141">Clavier</span><span class="sxs-lookup"><span data-stu-id="559ee-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="559ee-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="559ee-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="559ee-143">Souris</span><span class="sxs-lookup"><span data-stu-id="559ee-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="559ee-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="559ee-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="559ee-145">Pointeur de souris</span><span class="sxs-lookup"><span data-stu-id="559ee-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="559ee-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="559ee-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="559ee-147">Roulette de la souris</span><span class="sxs-lookup"><span data-stu-id="559ee-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="559ee-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="559ee-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="559ee-149">Avancement</span><span class="sxs-lookup"><span data-stu-id="559ee-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="559ee-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="559ee-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="559ee-151">Entrées tactiles</span><span class="sxs-lookup"><span data-stu-id="559ee-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="559ee-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="559ee-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="559ee-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="559ee-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="559ee-154">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="559ee-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="559ee-155">[Classes EventArgs dans la source de référence ASP.net Core (branche dotnet/aspnetcore ou version 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="559ee-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="559ee-156">[MDN Web docs : GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): contient des informations sur les éléments HTML qui prennent en charge chaque événement DOM.</span><span class="sxs-lookup"><span data-stu-id="559ee-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="559ee-157">Expressions lambda</span><span class="sxs-lookup"><span data-stu-id="559ee-157">Lambda expressions</span></span>

<span data-ttu-id="559ee-158">Les [expressions lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :</span><span class="sxs-lookup"><span data-stu-id="559ee-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="559ee-159">Il est souvent pratique de se rapprocher de valeurs supplémentaires, par exemple lors de l’itération sur un ensemble d’éléments.</span><span class="sxs-lookup"><span data-stu-id="559ee-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="559ee-160">L’exemple suivant crée trois boutons, chacun d’entre eux qui `UpdateHeading` passe un argument d’événement ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) et son numéro de bouton ( `buttonNumber` ) lorsqu’ils sont sélectionnés dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="559ee-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="559ee-161">N’utilisez **pas** directement une variable de boucle dans une expression lambda, comme `i` dans l' `for` exemple de boucle précédent ou une variable de référence dans une `foreach` boucle.</span><span class="sxs-lookup"><span data-stu-id="559ee-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="559ee-162">Dans le cas contraire, la même variable est utilisée par toutes les expressions lambda, ce qui entraîne l’utilisation de la même valeur dans toutes les expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="559ee-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="559ee-163">Capturez toujours la valeur de la variable dans une variable locale, puis utilisez-la.</span><span class="sxs-lookup"><span data-stu-id="559ee-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="559ee-164">Dans l’exemple précédent, la variable `i` de boucle est assignée à `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="559ee-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="559ee-165">EventCallback suivante</span><span class="sxs-lookup"><span data-stu-id="559ee-165">EventCallback</span></span>

<span data-ttu-id="559ee-166">Un scénario courant avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de composant enfant se produit.</span><span class="sxs-lookup"><span data-stu-id="559ee-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="559ee-167">Un `onclick` événement qui se produit dans le composant enfant est un cas d’usage courant.</span><span class="sxs-lookup"><span data-stu-id="559ee-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="559ee-168">Pour exposer des événements entre les composants, utilisez un <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="559ee-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="559ee-169">Un composant parent peut affecter une méthode de rappel à un composant enfant <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="559ee-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="559ee-170">`ChildComponent`Dans l’exemple d’application (*Components/ChildComponent. Razor*), vous montre comment un gestionnaire de bouton `onclick` est configuré pour recevoir un <xref:Microsoft.AspNetCore.Components.EventCallback> délégué de l’exemple de `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="559ee-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="559ee-171">Le <xref:Microsoft.AspNetCore.Components.EventCallback> est typé avec `MouseEventArgs` , ce qui est approprié pour un `onclick` événement à partir d’un périphérique :</span><span class="sxs-lookup"><span data-stu-id="559ee-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="559ee-172">`ParentComponent`Définit le () de l’enfant <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` sur sa `ShowMessage` méthode.</span><span class="sxs-lookup"><span data-stu-id="559ee-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="559ee-173">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="559ee-173">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="559ee-174">Lorsque le bouton est sélectionné dans le `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="559ee-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="559ee-175">La `ParentComponent` `ShowMessage` méthode de est appelée.</span><span class="sxs-lookup"><span data-stu-id="559ee-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="559ee-176">`messageText`est mis à jour et affiché dans le `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="559ee-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="559ee-177">Un appel à [StateHasChanged](xref:blazor/components/lifecycle#state-changes) n’est pas requis dans la méthode du rappel ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="559ee-177">A call to [StateHasChanged](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="559ee-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé automatiquement pour rerestituer le `ParentComponent` , tout comme les événements enfants déclenchent le rerendu des composants dans les gestionnaires d’événements qui s’exécutent dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="559ee-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="559ee-179"><xref:Microsoft.AspNetCore.Components.EventCallback>et <xref:Microsoft.AspNetCore.Components.EventCallback%601> autorisent les délégués asynchrones.</span><span class="sxs-lookup"><span data-stu-id="559ee-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="559ee-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601>est fortement typé et requiert un type d’argument spécifique.</span><span class="sxs-lookup"><span data-stu-id="559ee-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="559ee-181"><xref:Microsoft.AspNetCore.Components.EventCallback>est faiblement typé et autorise tout type d’argument.</span><span class="sxs-lookup"><span data-stu-id="559ee-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="559ee-182">Appelez <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> avec <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> et en attente de <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="559ee-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="559ee-183">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> et <xref:Microsoft.AspNetCore.Components.EventCallback%601> pour la gestion des événements et les paramètres de composant de liaison.</span><span class="sxs-lookup"><span data-stu-id="559ee-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="559ee-184">Préférez le fortement typé <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="559ee-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="559ee-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fournit un meilleur retour d’erreur aux utilisateurs du composant.</span><span class="sxs-lookup"><span data-stu-id="559ee-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="559ee-186">Comme pour d’autres gestionnaires d’événements d’interface utilisateur, la spécification du paramètre d’événement est facultative.</span><span class="sxs-lookup"><span data-stu-id="559ee-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="559ee-187">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> quand aucune valeur n’est passée au rappel.</span><span class="sxs-lookup"><span data-stu-id="559ee-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="559ee-188">Empêcher les actions par défaut</span><span class="sxs-lookup"><span data-stu-id="559ee-188">Prevent default actions</span></span>

<span data-ttu-id="559ee-189">Utilisez l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attribut directive pour empêcher l’action par défaut d’un événement.</span><span class="sxs-lookup"><span data-stu-id="559ee-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="559ee-190">Quand une clé est sélectionnée sur un appareil d’entrée et que le focus de l’élément se trouve sur une zone de texte, un navigateur affiche normalement le caractère de la clé dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="559ee-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="559ee-191">Dans l’exemple suivant, le comportement par défaut est évité en spécifiant l' `@onkeypress:preventDefault` attribut directive.</span><span class="sxs-lookup"><span data-stu-id="559ee-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="559ee-192">Le compteur est incrémenté et la **+** clé n’est pas capturée dans la `<input>` valeur de l’élément :</span><span class="sxs-lookup"><span data-stu-id="559ee-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="559ee-193">La spécification de l' `@on{EVENT}:preventDefault` attribut sans valeur équivaut à `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="559ee-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="559ee-194">La valeur de l’attribut peut également être une expression.</span><span class="sxs-lookup"><span data-stu-id="559ee-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="559ee-195">Dans l’exemple suivant, `shouldPreventDefault` est un `bool` champ défini sur `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="559ee-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="559ee-196">Un gestionnaire d’événements n’est pas nécessaire pour empêcher l’action par défaut.</span><span class="sxs-lookup"><span data-stu-id="559ee-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="559ee-197">Le gestionnaire d’événements et les scénarios d’action par défaut peuvent être utilisés indépendamment.</span><span class="sxs-lookup"><span data-stu-id="559ee-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="559ee-198">Arrêter la propagation des événements</span><span class="sxs-lookup"><span data-stu-id="559ee-198">Stop event propagation</span></span>

<span data-ttu-id="559ee-199">Utilisez l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attribut directive pour arrêter la propagation des événements.</span><span class="sxs-lookup"><span data-stu-id="559ee-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="559ee-200">Dans l’exemple suivant, la sélection de la case à cocher empêche les événements Click du deuxième enfant `<div>` de se propager vers le parent `<div>` :</span><span class="sxs-lookup"><span data-stu-id="559ee-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
