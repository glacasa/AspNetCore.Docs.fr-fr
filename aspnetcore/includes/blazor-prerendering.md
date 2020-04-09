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
Alors Blazor qu’une application Server est pré-implantation, certaines actions, telles que l’appel dans JavaScript, ne sont pas possibles parce qu’une connexion avec le navigateur n’a pas été établie. Les composants peuvent devoir rendre différemment lorsqu’ils sont préditeurs.

Pour retarder les appels interop JavaScript jusqu’à ce que la connexion avec le navigateur soit établie, vous pouvez utiliser [l’événement sur le cycle de vie des composants OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). Cet événement n’est appelé qu’après que l’application est entièrement rendue et que la connexion client est établie.

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

Pour l’exemple précédent, `setElementText` fournissez une `<head>` fonction JavaScript àBlazor l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Blazor Serveur). La fonction est `IJSRuntime.InvokeVoidAsync` appelée avec et ne retourne pas une valeur:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> L’exemple précédent modifie directement le modèle d’objet de document (DOM) à des fins de démonstration seulement. Modifier directement le DOM avec JavaScript n’est pas recommandé dans Blazorla plupart des scénarios parce que JavaScript peut interférer avec le suivi des modifications.

Le composant suivant montre comment utiliser l’interop JavaScript dans le cadre de la logique d’initialisation d’un composant d’une manière compatible avec le prérendering. Le composant montre qu’il est possible de `OnAfterRenderAsync`déclencher une mise à jour de rendu de l’intérieur . Le développeur doit éviter de créer une boucle infinie dans ce scénario.

Où `JSRuntime.InvokeAsync` est `ElementRef` appelé, `OnAfterRenderAsync` est seulement utilisé dans et non pas dans une méthode de cycle de vie plus tôt parce qu’il n’y a pas d’élément JavaScript jusqu’à ce que le composant est rendu.

[StateHasChanged](xref:blazor/lifecycle#state-changes) est appelé à rerender le composant avec le nouvel état obtenu de l’appel InterOp JavaScript. Le code ne crée pas `StateHasChanged` une boucle `infoFromJs` infinie parce qu’il n’est appelé que lorsqu’il est `null`.

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

Pour l’exemple précédent, `setElementText` fournissez une `<head>` fonction JavaScript àBlazor l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* (Blazor Serveur). La fonction est `IJSRuntime.InvokeAsync` appelée avec et retourne une valeur:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> L’exemple précédent modifie directement le modèle d’objet de document (DOM) à des fins de démonstration seulement. Modifier directement le DOM avec JavaScript n’est pas recommandé dans Blazorla plupart des scénarios parce que JavaScript peut interférer avec le suivi des modifications.
