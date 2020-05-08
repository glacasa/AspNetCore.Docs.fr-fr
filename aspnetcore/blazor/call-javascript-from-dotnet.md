---
title: Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment appeler des fonctions JavaScript à partir de méthodes Blazor .net dans des applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 064f504e94cd65862370d4551c6cb44210a8238f
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967296"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET CoreBlazor

Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Une Blazor application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript. Ces scénarios portent le nom de *l’interopérabilité de JavaScript* (*js Interop*).

Cet article traite de l’appel de fonctions JavaScript à partir de .NET. Pour plus d’informations sur la façon d’appeler des méthodes .NET <xref:blazor/call-dotnet-from-javascript>à partir de JavaScript, consultez.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Pour appeler JavaScript à partir de .NET, utilisez `IJSRuntime` l’abstraction. Pour émettre des appels d’interopérabilité JS `IJSRuntime` , injectez l’abstraction dans votre composant. La `InvokeAsync<T>` méthode prend un identificateur pour la fonction JavaScript que vous souhaitez appeler, ainsi que n’importe quel nombre d’arguments sérialisables JSON. L’identificateur de fonction est relatif à la portée globale (`window`). Si vous souhaitez appeler `window.someScope.someFunction`, l’identificateur est. `someScope.someFunction` Il n’est pas nécessaire d’inscrire la fonction avant qu’elle ne soit appelée. Le type `T` de retour doit également être sérialisable JSON. `T`doit correspondre au type .NET qui correspond le mieux au type JSON retourné.

Pour Blazor les applications serveur avec prérendu activé, l’appel à JavaScript n’est pas possible pendant le prérendu initial. Les appels Interop JavaScript doivent être différés jusqu’à ce que la connexion avec le navigateur soit établie. Pour plus d’informations, consultez la section [détecter Blazor quand une application serveur est un prérendu](#detect-when-a-blazor-server-app-is-prerendering) .

L’exemple suivant est basé sur [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un décodeur basé sur JavaScript. L’exemple montre comment appeler une fonction JavaScript à partir d’une méthode C#. La fonction JavaScript accepte un tableau d’octets d’une méthode C#, décode le tableau et retourne le texte au composant pour l’affichage.

À l' `<head>` intérieur de l’élémentBlazor de *wwwroot/index.html* (webassembly) ou de *pages/_Host. cshtml* (Blazor Server), fournissez une fonction JavaScript qui utilise `TextDecoder` pour décoder un tableau passé et retourner la valeur décodée :

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Du code JavaScript, tel que le code illustré dans l’exemple précédent, peut également être chargé à partir d’un fichier JavaScript (*. js*) avec une référence au fichier de script :

```html
<script src="exampleJsInterop.js"></script>
```

Le composant suivant :

* Appelle la `convertArray` fonction JavaScript à l' `JSRuntime` aide de lorsqu’un bouton de composant (**convertir un tableau**) est sélectionné.
* Une fois la fonction JavaScript appelée, le tableau passé est converti en chaîne. La chaîne est retournée au composant pour l’affichage.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Pour utiliser l' `IJSRuntime` abstraction, adoptez l’une des approches suivantes :

* Injecter `IJSRuntime` l’abstraction dans Razor le composant (*. Razor*) :

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  À l' `<head>` intérieur de l’élémentBlazor de *wwwroot/index.html* (webassembly) ou de *pages/_Host. cshtml* Blazor ( `handleTickerChanged` Server), fournissez une fonction JavaScript. La fonction est appelée avec `IJSRuntime.InvokeVoidAsync` et ne retourne pas de valeur :

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Injecter `IJSRuntime` l’abstraction dans une classe (*. cs*) :

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  À l' `<head>` intérieur de l’élémentBlazor de *wwwroot/index.html* (webassembly) ou de *pages/_Host. cshtml* Blazor ( `handleTickerChanged` Server), fournissez une fonction JavaScript. La fonction est appelée avec `JSRuntime.InvokeAsync` et retourne une valeur :

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Pour la génération de contenu dynamique avec [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), `[Inject]` utilisez l’attribut :

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

Dans l’exemple d’application côté client qui accompagne cette rubrique, deux fonctions JavaScript sont disponibles pour l’application qui interagit avec le DOM pour recevoir l’entrée d’utilisateur et afficher un message d’accueil :

* `showPrompt`&ndash; Génère une invite pour accepter l’entrée d’utilisateur (nom de l’utilisateur) et retourne le nom à l’appelant.
* `displayWelcome`&ndash; Assigne un message de bienvenue de l’appelant à un objet DOM avec `id` un `welcome`de.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Placez la `<script>` balise qui fait référence au fichier JavaScript *wwwroot/index.html* dans le fichierBlazor wwwroot/index.html (webassembly) ou le fichier *pages/_Host. cshtml* (Blazor serveur).

*wwwroot/index.html* (Blazor webassembly) :

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host. cshtml* (Blazor serveur) :

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Ne placez pas `<script>` de balise dans un fichier de `<script>` composant parce que la balise ne peut pas être mise à jour dynamiquement.

Les méthodes .NET interagissent avec les fonctions JavaScript dans le fichier *exampleJsInterop. js* en appelant `IJSRuntime.InvokeAsync<T>`.

L' `IJSRuntime` abstraction est asynchrone pour permettre les Blazor scénarios de serveur. Si l’application est une Blazor application webassembly et que vous souhaitez appeler une fonction JavaScript de manière synchrone, `IJSInProcessRuntime` vous devez `Invoke<T>` effectuer un cast aval vers et appeler à la place. Nous recommandons que la plupart des bibliothèques d’interopérabilité JS utilisent les API Async pour s’assurer que les bibliothèques sont disponibles dans tous les scénarios.

L’exemple d’application comprend un composant pour illustrer l’interopérabilité de JS. Le composant :

* Reçoit une entrée d’utilisateur via une invite JavaScript.
* Retourne le texte du composant pour traitement.
* Appelle une deuxième fonction JavaScript qui interagit avec le DOM pour afficher un message d’accueil.

*Pages/JSInterop. Razor*:

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

1. Lorsque `TriggerJsPrompt` est exécuté en sélectionnant le bouton d' **invite de commandes JavaScript** du composant `showPrompt` , la fonction JavaScript fournie dans le fichier *wwwroot/exampleJsInterop. js* est appelée.
1. La `showPrompt` fonction accepte l’entrée d’utilisateur (nom de l’utilisateur), qui est codée au format HTML et renvoyée au composant. Le composant stocke le nom de l’utilisateur dans une variable `name`locale,.
1. La chaîne stockée `name` dans est incorporée dans un message de bienvenue, qui est transmis à une `displayWelcome`fonction JavaScript,, qui affiche le message de bienvenue dans une balise de titre.

## <a name="call-a-void-javascript-function"></a>Appeler une fonction JavaScript void

Les fonctions JavaScript qui retournent [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) sont appelées avec `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>Détecter quand une Blazor application serveur est prérendu
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Capturer des références à des éléments

Certains scénarios d’interopérabilité JS requièrent des références à des éléments HTML. Par exemple, une bibliothèque d’interface utilisateur peut nécessiter une référence d’élément pour l’initialisation, ou vous devrez peut-être appeler des API de type commande `focus` sur `play`un élément, tel que ou.

Capturer des références aux éléments HTML dans un composant à l’aide de l’approche suivante :

* Ajoutez un `@ref` attribut à l’élément HTML.
* Définissez un champ de type `ElementReference` dont le nom correspond à la valeur `@ref` de l’attribut.

L’exemple suivant illustre la capture d’une référence `username` `<input>` à l’élément :

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Utilisez uniquement une référence d’élément pour muter le contenu d’un élément vide qui n’interagit Blazorpas avec. Ce scénario est utile quand une API tierce fournit du contenu à l’élément. Étant Blazor donné que n’interagit pas avec l’élément, il n’existe aucun Blazorrisque de conflit entre la représentation de l’élément et le DOM.
>
> Dans l’exemple suivant, il est *dangereux* de faire muter le contenu de la liste non triée (`ul`), Blazor car interagit avec le DOM pour remplir les éléments de liste de`<li>`cet élément () :
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
> Si l’interopérabilité JS muter le contenu de `MyList` l' Blazor élément et tente d’appliquer des différences à l’élément, les différences ne correspondent pas au DOM.

En ce qui concerne le code .NET, un `ElementReference` est un handle opaque. La *seule* chose que vous pouvez faire `ElementReference` avec est de la passer au code JavaScript via l’interopérabilité de js. Dans ce cas, le code JavaScript reçoit une `HTMLElement` instance, qu’il peut utiliser avec les API DOM normales.

Par exemple, le code suivant définit une méthode d’extension .NET qui permet de définir le focus sur un élément :

*exampleJsInterop. js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Pour appeler une fonction JavaScript qui ne retourne pas de valeur, `IJSRuntime.InvokeVoidAsync`utilisez. Le code suivant définit le focus sur l’entrée de nom d’utilisateur en appelant la fonction JavaScript précédente `ElementReference`avec le capturé :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Pour utiliser une méthode d’extension, créez une méthode d’extension statique qui `IJSRuntime` reçoit l’instance :

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

La `Focus` méthode est appelée directement sur l’objet. L’exemple suivant suppose que la `Focus` méthode est disponible à partir de `JsInteropClasses` l’espace de noms :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> La `username` variable est remplie uniquement après le rendu du composant. Si un non rempli `ElementReference` est passé à du code JavaScript, le code JavaScript reçoit la valeur `null`. Pour manipuler des références d’élément après que le composant a terminé le rendu (pour définir le focus initial sur un élément), utilisez les [méthodes de cycle de vie des composants OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).

Lors de l’utilisation de types génériques et du retour d’une valeur, utilisez [\<ValueTask T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`est appelé directement sur l’objet avec un type. L’exemple suivant suppose que le `GenericMethod` est disponible à partir de `JsInteropClasses` l’espace de noms :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Éléments de référence sur les composants

Un `ElementReference` est garanti uniquement valide dans la méthode d' `OnAfterRender` un composant (et une référence d’élément `struct`est un), de sorte qu’une référence d’élément ne peut pas être transmise entre les composants.

Pour qu’un composant parent rende une référence d’élément disponible pour d’autres composants, le composant parent peut :

* Autorisez les composants enfants à inscrire des rappels.
* Appelez les rappels inscrits pendant l' `OnAfterRender` événement avec la référence d’élément passée. Indirectement, cette approche permet aux composants enfants d’interagir avec la référence d’élément du parent.

L’exemple Blazor webassembly suivant illustre l’approche.

Dans le `<head>` de *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

Dans le `<body>` de *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/index. Razor* (composant parent) :

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/index. Razor. cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

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
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Shared/SurveyPrompt. Razor* (composant enfant) :

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

*Shared/SurveyPrompt. Razor. cs*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Sécuriser les appels d’interopérabilité JS

L’interopérabilité de JS peut échouer en raison d’erreurs réseau et doit être considérée comme non fiable. Par défaut, une Blazor application serveur expire les appels d’interopérabilité de js sur le serveur après une minute. Si une application peut tolérer un délai d’expiration plus agressif, par exemple 10 secondes, définissez le délai d’expiration à l’aide de l’une des approches suivantes :

* Globalement dans `Startup.ConfigureServices`, spécifiez le délai d’expiration :

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Par appel dans le code du composant, un appel unique peut spécifier le délai d’attente :

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Pour plus d’informations sur l’épuisement des <xref:security/blazor/server/threat-mitigation>ressources, consultez.

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Éviter les références d’objets circulaires

Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :

* Appels de méthode .NET.
* Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.

Pour plus d’informations, consultez les problèmes suivants :

* [Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/call-dotnet-from-javascript>
* [Exemple InteropComponent. Razor (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Effectuer des transferts de données Blazor volumineux dans des applications serveur](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
