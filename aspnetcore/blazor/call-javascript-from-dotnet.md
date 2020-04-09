---
title: Appelez les fonctions JavaScript à partir de méthodes .NET dans ASP.NET CoreBlazor
author: guardrex
description: Apprenez à invoquer les fonctions Blazor JavaScript à partir de méthodes .NET dans les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977013"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>Appelez les fonctions JavaScript à partir de méthodes .NET dans ASP.NET CoreBlazor

Par [Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Une Blazor application peut invoquer les fonctions JavaScript à partir de méthodes .NET et de méthodes .NET à partir des fonctions JavaScript. Ces scénarios sont appelés *Interopability JavaScript* (*JS interop*).

Cet article couvre l’invocation des fonctions JavaScript de .NET. Pour plus d’informations sur la façon <xref:blazor/call-dotnet-from-javascript>d’appeler les méthodes .NET de JavaScript, voir .

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

Pour appeler JavaScript à partir `IJSRuntime` de .NET, utilisez l’abstraction. Pour émettre des appels interop JS, injectez l’abstraction `IJSRuntime` dans votre composant. La `InvokeAsync<T>` méthode prend un identifiant pour la fonction JavaScript que vous souhaitez invoquer avec un certain nombre d’arguments JSON-sérialisable. L’identifiant de fonction est`window`relatif à la portée globale (). Si vous souhaitez `window.someScope.someFunction`appeler, `someScope.someFunction`l’identifiant est . Il n’est pas nécessaire d’enregistrer la fonction avant qu’elle ne s’appelle. Le type `T` de retour doit également être sérialisable JSON. `T`devrait correspondre au type .NET que les meilleures cartes au type JSON retourné.

Pour Blazor les applications Server avec prerendering activé, appeler dans JavaScript n’est pas possible lors du prérendering initial. Les appels interop JavaScript doivent être reportés jusqu’à ce que la connexion avec le navigateur soit établie. Pour plus d’informations, voir la [détection Blazor lorsqu’une application Server est la section de préréaender.](#detect-when-a-blazor-server-app-is-prerendering)

L’exemple suivant est basé sur [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un décodeur basé sur JavaScript. L’exemple montre comment invoquer une fonction JavaScript à partir d’une méthode C. La fonction JavaScript accepte un tableau d’ord d’une méthode C, décode le tableau et renvoie le texte au composant pour l’affichage.

À `<head>` l’intérieur deBlazor l’élément de *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* Blazor ( `TextDecoder` Server), fournir une fonction JavaScript qui utilise pour décoder un tableau passé et retourner la valeur décodée:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Le code JavaScript, tel que le code indiqué dans l’exemple précédent, peut également être chargé à partir d’un fichier JavaScript (*.js*) avec une référence au fichier script :

```html
<script src="exampleJsInterop.js"></script>
```

Le composant suivant :

* Invoque `convertArray` la fonction `JSRuntime` JavaScript à l’aide de la sélection d’un bouton composant (**Convert Array).**
* Après que la fonction JavaScript est appelée, le tableau passé est converti en une chaîne. La chaîne est retournée au composant pour l’affichage.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Pour utiliser `IJSRuntime` l’abstraction, adoptez l’une des approches suivantes :

* Injecter `IJSRuntime` l’abstraction dans le composant Razor (*.razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  À `<head>` l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly)Blazor ou *Pages/_Host.cshtml* (Blazor Server), fournir une `handleTickerChanged` fonction JavaScript. La fonction est `IJSRuntime.InvokeVoidAsync` appelée avec et ne retourne pas une valeur:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Injecter `IJSRuntime` l’abstraction dans une classe (*.cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  À `<head>` l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly)Blazor ou *Pages/_Host.cshtml* (Blazor Server), fournir une `handleTickerChanged` fonction JavaScript. La fonction est `JSRuntime.InvokeAsync` appelée avec et retourne une valeur:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Pour la génération de contenu dynamique `[Inject]` avec [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), utilisez l’attribut :

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Dans l’application d’échantillon côté client qui accompagne ce sujet, deux fonctions JavaScript sont disponibles pour l’application qui interagissent avec le DOM pour recevoir l’entrée de l’utilisateur et afficher un message de bienvenue :

* `showPrompt`&ndash; Produit une invite à accepter l’entrée de l’utilisateur (nom de l’utilisateur) et renvoie le nom à l’appelant.
* `displayWelcome`&ndash; Assigne un message de bienvenue de l’appelant à un objet DOM avec un `id` de `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Placez `<script>` l’étiquette qui fait référence au fichierBlazor JavaScript dans le fichier *wwwroot/index.html* (WebAssembly) ouBlazor *Pages/_Host.cshtml* fichier (Serveur).

*wwwroot/index.html* (WebAssembly):Blazor

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host.cshtml* (Serveur)Blazor :

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Ne placez `<script>` pas une étiquette dans `<script>` un fichier de composant parce que l’étiquette ne peut pas être mise à jour dynamiquement.

.NET méthodes interop avec les fonctions JavaScript dans le fichier `IJSRuntime.InvokeAsync<T>` *exampleJsInterop.js* en appelant .

L’abstraction `IJSRuntime` est asynchrone Blazor pour permettre des scénarios Server. Si l’application Blazor est une application WebAssembly et que vous souhaitez invoquer `Invoke<T>` une fonction JavaScript de façon synchronisée, abattue et appeler à `IJSInProcessRuntime` la place. Nous recommandons que la plupart des bibliothèques interop JS utilisent les API async pour s’assurer que les bibliothèques sont disponibles dans tous les scénarios.

L’application d’échantillon inclut un composant pour démontrer JS interop. Le composant:

* Reçoit l’entrée de l’utilisateur via une invite JavaScript.
* Retourne le texte au composant pour le traitement.
* Appelle une deuxième fonction JavaScript qui interagit avec le DOM pour afficher un message de bienvenue.

*Pages/JSInterop.razor*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. Lorsqu’elle `TriggerJsPrompt` est exécutée en sélectionnant le bouton **Trigger JavaScript Prompt** du composant, la fonction JavaScript `showPrompt` fournie dans le fichier *wwwroot/exampleJsInterop.js* est appelée.
1. La `showPrompt` fonction accepte l’entrée de l’utilisateur (nom de l’utilisateur), qui est codée en HTML et retournée au composant. Le composant stocke le nom de `name`l’utilisateur dans une variable locale, .
1. La chaîne `name` stockée dans est incorporée dans un message de `displayWelcome`bienvenue, qui est passé à une fonction JavaScript, , qui rend le message de bienvenue dans une balise de titre.

## <a name="call-a-void-javascript-function"></a>Appelez une fonction JavaScript vide

Les fonctions JavaScript qui retournent [vide(0)/vide 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [indéfinis](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) sont appelées avec `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Détecter quand Blazor une application Server est prerendering
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Capturez les références aux éléments

Certains scénarios interop JS nécessitent des références à des éléments HTML. Par exemple, une bibliothèque d’interface utilisateur peut nécessiter une référence d’élément pour l’initialisation, ou vous pourriez avoir besoin d’appeler des API de commande sur un élément, tel que `focus` ou `play`.

Capturez les références aux éléments HTML d’un composant à l’aide de l’approche suivante :

* Ajoutez `@ref` un attribut à l’élément HTML.
* Définissez un `ElementReference` champ de type dont `@ref` le nom correspond à la valeur de l’attribut.

L’exemple suivant montre la `username` `<input>` capture d’une référence à l’élément :

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Utilisez uniquement une référence d’élément pour muter le contenu Blazord’un élément vide qui n’interagit pas avec . Ce scénario est utile lorsqu’une API de tiers fournit du contenu à l’élément. Parce Blazor que n’interagit pas avec l’élément, Blazoril n’y a aucune possibilité d’un conflit entre la représentation de l’élément et le DOM.
>
> Dans l’exemple suivant, il est *dangereux* de muter le`ul`contenu Blazor de la liste non ordonnée ( )`<li>`parce que les interagit avec le DOM pour remplir les éléments de liste de cet élément (
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Si JS interop mute `MyList` le Blazor contenu de l’élément et tente d’appliquer des diffs à l’élément, les diffs ne correspondent pas au DOM.

En ce qui concerne le `ElementReference` code .NET, un est une poignée opaque. La *seule* chose que `ElementReference` vous pouvez faire avec est de le passer à travers le code JavaScript via JS interop. Lorsque vous le faites, le code `HTMLElement` Côté JavaScript reçoit une instance, qu’il peut utiliser avec des API DOM normales.

Par exemple, le code suivant définit une méthode d’extension .NET qui permet de mettre l’accent sur un élément :

*exempleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Pour appeler une fonction JavaScript qui ne `IJSRuntime.InvokeVoidAsync`retourne pas une valeur, utilisez . Le code suivant met l’accent sur l’entrée du nom `ElementReference`d’utilisateur en appelant la fonction JavaScript précédente avec les personnes capturées :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Pour utiliser une méthode d’extension, créez `IJSRuntime` une méthode d’extension statique qui reçoit l’instance :

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

La `Focus` méthode est appelée directement sur l’objet. L’exemple suivant suppose `Focus` que la `JsInteropClasses` méthode est disponible à partir de l’espace nom:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> La `username` variable n’est peuplée qu’après la remise du composant. Si un nonpopulé `ElementReference` est passé au code JavaScript, `null`le code JavaScript reçoit une valeur de . Pour manipuler les références d’éléments après que le composant a fini le rendu (pour définir l’accent initial sur un élément) utiliser les [méthodes de cycle de vie du composant OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).

Lorsque vous travaillez avec des types génériques et retournez une valeur, utilisez [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`est appelé directement sur l’objet avec un type. L’exemple suivant suppose `GenericMethod` que le `JsInteropClasses` est disponible à partir de l’espace nom:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Éléments de référence entre les composants

Un `ElementReference` est seulement garanti valable `OnAfterRender` dans la méthode d’un composant (et une référence d’élément est un `struct`), ainsi une référence d’élément ne peut pas être passée entre les composants.

Pour qu’un composant parent fasse une référence d’élément accessible à d’autres composantes, le composant parent peut :

* Permettre aux composants pour enfants d’enregistrer les rappels.
* Invoquez les rappels enregistrés pendant l’événement `OnAfterRender` avec la référence de l’élément passé. Indirectement, cette approche permet aux composants pour enfants d’interagir avec la référence de l’élément du parent.

L’exemple WebAssembly suivant Blazor illustre l’approche.

Dans `<head>` le *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

Dans `<body>` le *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/Index.razor* (composant parent) :

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/Index.razor.cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Partagé/SurveyPrompt.razor* (composant enfant):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Partagé/SurveyPrompt.razor.cs*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Harden JS interop appels

JS interop peut échouer en raison d’erreurs de réseautage et doit être traitée comme peu fiable. Par défaut, Blazor une application Server passe en charge les appels JS interop sur le serveur après une minute. Si une application peut tolérer un délai d’attente plus agressif, comme 10 secondes, définissez le délai d’attente à l’aide d’une des approches suivantes :

* Globalement `Startup.ConfigureServices`en , spécifier le délai d’attente:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Per-invocation dans le code des composants, un seul appel peut spécifier le délai d’attente :

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Pour plus d’informations sur <xref:security/blazor/server>l’épuisement des ressources, voir .

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Évitez les références circulaires d’objets

Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour l’un ou l’autre:

* appels de méthode .NET.
* La méthode JavaScript appelle à partir de C lorsque le type de retour a des références circulaires.

Pour plus d’informations, voir les questions suivantes :

* [Les références circulaires ne sont pas prises en charge, en prennent deux (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposition : Ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor exemple (dotnet/AspNetCore GitHub référentiel, branche de sortie 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Effectuer de grands Blazor transferts de données dans les applications Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
