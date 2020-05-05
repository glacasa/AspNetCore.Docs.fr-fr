---
title: Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment appeler des méthodes .NET à partir de fonctions Blazor JavaScript dans des applications.
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 1bc75f0825b114a24def287bb7ccb11c27514f01
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767185"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a>Appeler des méthodes .NET à partir de fonctions JavaScript dans ASP.NET CoreBlazor

Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Une Blazor application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript. Ces scénarios portent le nom de *l’interopérabilité de JavaScript* (*js Interop*).

Cet article traite de l’appel de méthodes .NET à partir de JavaScript. Pour plus d’informations sur l’appel de fonctions JavaScript à partir <xref:blazor/call-javascript-from-dotnet>de .net, consultez.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Appel de méthode .NET statique

Pour appeler une méthode .NET statique à partir de JavaScript, `DotNet.invokeMethod` utilisez `DotNet.invokeMethodAsync` les fonctions ou. Transmettez l’identificateur de la méthode statique que vous souhaitez appeler, le nom de l’assembly contenant la fonction et les arguments éventuels. La version asynchrone est recommandée pour prendre Blazor en charge les scénarios de serveur. La méthode .NET doit être publique, statique et avoir l' `[JSInvokable]` attribut. L’appel de méthodes génériques ouvertes n’est pas pris en charge actuellement.

L’exemple d’application comprend une méthode C# pour retourner `int` un tableau. L' `JSInvokable` attribut est appliqué à la méthode.

*Pages/JsInterop. Razor*:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

JavaScript traité au client appelle la méthode .NET C#.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quand le bouton déclencher l’ReturnArrayAsync de la **méthode statique .net** est sélectionné, examinez la sortie de la console dans les outils de développement Web du navigateur.

La sortie de la console est la suivante :

```console
Array(4) [ 1, 2, 3, 4 ]
```

La quatrième valeur de tableau fait l’objet d’un`data.push(4);`push dans le `ReturnArrayAsync`tableau () retourné par.

Par défaut, l’identificateur de méthode est le nom de la méthode, mais vous pouvez spécifier un identificateur différent à `JSInvokableAttribute` l’aide du constructeur :

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

Dans le fichier JavaScript côté client :

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Appel de méthode d’instance

Vous pouvez également appeler des méthodes d’instance .NET à partir de JavaScript. Pour appeler une méthode d’instance .NET à partir de JavaScript :

* Passer l’instance .NET par référence à JavaScript :
  * Effectuez un appel statique à `DotNetObjectReference.Create`.
  * Encapsulez l’instance `DotNetObjectReference` dans une instance `Create` et appelez `DotNetObjectReference` sur l’instance. Supprimez `DotNetObjectReference` des objets (un exemple apparaît plus loin dans cette section).
* Appeler des méthodes d’instance .NET sur l’instance `invokeMethod` à `invokeMethodAsync` l’aide des fonctions ou. L’instance .NET peut également être passée comme argument lors de l’appel d’autres méthodes .NET à partir de JavaScript.

> [!NOTE]
> L’exemple d’application enregistre les messages dans la console côté client. Pour les exemples suivants présentés dans l’exemple d’application, examinez la sortie de console du navigateur dans les outils de développement du navigateur.

Lorsque le bouton de **méthode d’instance .net de déclenchement HelloHelper. SayHello** est sélectionné, `ExampleJsInterop.CallHelloHelperSayHello` est appelé et passe `Blazor`un nom,, à la méthode.

*Pages/JsInterop. Razor*:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello`appelle la fonction `sayHello` JavaScript avec une nouvelle instance de `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Le nom est passé au `HelloHelper`constructeur de, qui définit la `HelloHelper.Name` propriété. Lorsque la fonction `sayHello` JavaScript est exécutée `HelloHelper.SayHello` , retourne `Hello, {Name}!` le message, qui est écrit dans la console par la fonction JavaScript.

*JsInteropClasses/HelloHelper. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Sortie de la console dans les outils de développement Web du navigateur :

```console
Hello, Blazor!
```

Pour éviter une fuite de mémoire et autoriser garbage collection sur un composant qui crée `DotNetObjectReference`un, adoptez l’une des approches suivantes :

* Supprimez l’objet dans la classe qui a créé `DotNetObjectReference` l’instance :

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime _jsRuntime;
      private DotNetObjectReference<HelloHelper> _objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return _jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

  Le modèle précédent présenté dans la `ExampleJsInterop` classe peut également être implémenté dans un composant :

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> _objRef;

      public async Task TriggerNetInstanceMethod()
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

* Lorsque le composant ou la classe ne supprime pas `DotNetObjectReference`le, supprimez l’objet sur le client en `.dispose()`appelant :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Appel de méthode d’instance de composant

Pour appeler les méthodes .NET d’un composant :

* Utilisez la `invokeMethod` fonction `invokeMethodAsync` ou pour effectuer un appel de méthode statique au composant.
* La méthode statique du composant encapsule l’appel à sa méthode d’instance en tant que `Action`appelé.

Dans le code JavaScript côté client :

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Pages/JSInteropComponent. Razor*:

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

Lorsqu’il existe plusieurs composants, chacun avec des méthodes d’instance à appeler, utilisez une classe d’assistance pour appeler les méthodes d' `Action`instance (en tant que s) de chaque composant.

Dans l’exemple suivant :

* Le `JSInterop` composant contient plusieurs `ListItem` composants.
* Chaque `ListItem` composant se compose d’un message et d’un bouton.
* Quand un `ListItem` bouton de composant est sélectionné, `ListItem`la `UpdateMessage` méthode modifie le texte de l’élément de liste et masque le bouton.

*MessageUpdateInvokeHelper.cs*:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

Dans le code JavaScript côté client :

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Shared/ListItem. Razor*:

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

*Pages/JSInterop. Razor*:

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Éviter les références d’objets circulaires

Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :

* Appels de méthode .NET.
* Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.

Pour plus d’informations, consultez les problèmes suivants :

* [Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/call-javascript-from-dotnet>
* [Exemple InteropComponent. Razor (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Effectuer des transferts de données Blazor volumineux dans des applications serveur](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
