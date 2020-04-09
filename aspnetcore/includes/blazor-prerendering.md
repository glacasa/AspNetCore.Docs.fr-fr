---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659718"
---
<span data-ttu-id="981a5-101">Alors Blazor qu’une application Server est pré-implantation, certaines actions, telles que l’appel dans JavaScript, ne sont pas possibles parce qu’une connexion avec le navigateur n’a pas été établie.</span><span class="sxs-lookup"><span data-stu-id="981a5-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="981a5-102">Les composants peuvent devoir rendre différemment lorsqu’ils sont préditeurs.</span><span class="sxs-lookup"><span data-stu-id="981a5-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="981a5-103">Pour retarder les appels interop JavaScript jusqu’à ce que la connexion avec le navigateur soit établie, vous pouvez utiliser [l’événement sur le cycle de vie des composants OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="981a5-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="981a5-104">Cet événement n’est appelé qu’après que l’application est entièrement rendue et que la connexion client est établie.</span><span class="sxs-lookup"><span data-stu-id="981a5-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="981a5-105">Pour l’exemple précédent, `setElementText` fournissez une `<head>` fonction JavaScript àBlazor l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Blazor Serveur).</span><span class="sxs-lookup"><span data-stu-id="981a5-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="981a5-106">La fonction est `IJSRuntime.InvokeVoidAsync` appelée avec et ne retourne pas une valeur:</span><span class="sxs-lookup"><span data-stu-id="981a5-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="981a5-107">L’exemple précédent modifie directement le modèle d’objet de document (DOM) à des fins de démonstration seulement.</span><span class="sxs-lookup"><span data-stu-id="981a5-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="981a5-108">Modifier directement le DOM avec JavaScript n’est pas recommandé dans Blazorla plupart des scénarios parce que JavaScript peut interférer avec le suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="981a5-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="981a5-109">Le composant suivant montre comment utiliser l’interop JavaScript dans le cadre de la logique d’initialisation d’un composant d’une manière compatible avec le prérendering.</span><span class="sxs-lookup"><span data-stu-id="981a5-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="981a5-110">Le composant montre qu’il est possible de `OnAfterRenderAsync`déclencher une mise à jour de rendu de l’intérieur .</span><span class="sxs-lookup"><span data-stu-id="981a5-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="981a5-111">Le développeur doit éviter de créer une boucle infinie dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="981a5-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="981a5-112">Où `JSRuntime.InvokeAsync` est `ElementRef` appelé, `OnAfterRenderAsync` est seulement utilisé dans et non pas dans une méthode de cycle de vie plus tôt parce qu’il n’y a pas d’élément JavaScript jusqu’à ce que le composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="981a5-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="981a5-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) est appelé à rerender le composant avec le nouvel état obtenu de l’appel InterOp JavaScript.</span><span class="sxs-lookup"><span data-stu-id="981a5-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="981a5-114">Le code ne crée pas `StateHasChanged` une boucle `infoFromJs` infinie parce qu’il n’est appelé que lorsqu’il est `null`.</span><span class="sxs-lookup"><span data-stu-id="981a5-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="981a5-115">Pour l’exemple précédent, `setElementText` fournissez une `<head>` fonction JavaScript àBlazor l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Blazor Serveur).</span><span class="sxs-lookup"><span data-stu-id="981a5-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="981a5-116">La fonction est `IJSRuntime.InvokeAsync` appelée avec et retourne une valeur:</span><span class="sxs-lookup"><span data-stu-id="981a5-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="981a5-117">L’exemple précédent modifie directement le modèle d’objet de document (DOM) à des fins de démonstration seulement.</span><span class="sxs-lookup"><span data-stu-id="981a5-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="981a5-118">Modifier directement le DOM avec JavaScript n’est pas recommandé dans Blazorla plupart des scénarios parce que JavaScript peut interférer avec le suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="981a5-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
