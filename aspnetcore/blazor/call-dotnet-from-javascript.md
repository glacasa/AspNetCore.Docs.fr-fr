---
title: Appelez les méthodes .NET des fonctions JavaScript dans ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment invoquer les méthodes .NET des fonctions JavaScript dans Blazor les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e2344dd15efd243a405373b6cf0362f28b48173a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976948"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>Appelez les méthodes .NET des fonctions JavaScript dans ASP.NET CoreBlazor

Par [Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Une Blazor application peut invoquer les fonctions JavaScript à partir de méthodes .NET et de méthodes .NET à partir des fonctions JavaScript. Ces scénarios sont appelés *Interopability JavaScript* (*JS interop*).

Cet article couvre l’invocation des méthodes .NET de JavaScript. Pour plus d’informations sur la façon d’appeler les fonctions JavaScript à partir de .NET, voir <xref:blazor/call-javascript-from-dotnet>.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Appel statique de méthode .NET

Pour invoquer une méthode statique .NET `DotNet.invokeMethodAsync` de JavaScript, utilisez le ou les `DotNet.invokeMethod` fonctions. Passez dans l’identifiant de la méthode statique que vous souhaitez appeler, le nom de l’assemblage contenant la fonction, et tous les arguments. La version asynchrone est Blazor préférée pour prendre en charge les scénarios Server. La méthode .NET doit être publique, `[JSInvokable]` statique, et avoir l’attribut. Appeler des méthodes génériques ouvertes n’est pas actuellement pris en charge.

L’application d’échantillon inclut une `int` méthode de C pour retourner un tableau. L’attribut `JSInvokable` est appliqué à la méthode.

*Pages/JsInterop.razor*:

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

JavaScript servi au client invoque la méthode C .NET.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Lorsque la **méthode statique Trigger .NET ReturnArrayAsync** bouton est sélectionné, examiner la sortie de la console dans les outils de développeur web du navigateur.

La sortie de la console est :

```console
Array(4) [ 1, 2, 3, 4 ]
```

La valeur du quatrième tableau`data.push(4);`est poussée `ReturnArrayAsync`à la gamme ( ) retourné par .

Par défaut, l’identifiant de méthode est le nom de `JSInvokableAttribute` la méthode, mais vous pouvez spécifier un identifiant différent à l’aide du constructeur :

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

Dans le fichier JavaScript côté client :

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

Vous pouvez également appeler des méthodes d’instance .NET de JavaScript. Pour invoquer une méthode d’instance .NET de JavaScript :

* Passez l’instance .NET par référence à JavaScript:
  * Faites un appel `DotNetObjectReference.Create`statique à .
  * Enveloppez l’instance dans un `DotNetObjectReference` cas et faites appel `Create` à l’instance. `DotNetObjectReference` Disposer `DotNetObjectReference` d’objets (un exemple apparaît plus tard dans cette section).
* Invoquez des méthodes d’instance .NET sur l’instance à l’aide de la ou `invokeMethod` `invokeMethodAsync` des fonctions. L’instance .NET peut également être adoptée comme un argument lors de l’invocation d’autres méthodes .NET de JavaScript.

> [!NOTE]
> L’application échantillon enregistre des messages à la console côté client. Pour les exemples suivants démontrés par l’application d’échantillon, examinez la sortie de la console du navigateur dans les outils de développeur du navigateur.

Lorsque la **méthode d’instance Trigger .NET HelloHelper.SayHello** bouton est sélectionné, `ExampleJsInterop.CallHelloHelperSayHello` est appelé et passe un nom, `Blazor`, à la méthode.

*Pages/JsInterop.razor*:

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

`CallHelloHelperSayHello`invoque la `sayHello` fonction JavaScript `HelloHelper`avec une nouvelle instance de .

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Le nom est `HelloHelper`transmis au constructeur de `HelloHelper.Name` 's, qui définit la propriété. Lorsque la fonction `sayHello` JavaScript `HelloHelper.SayHello` est `Hello, {Name}!` exécutée, renvoie le message, qui est écrit sur la console par la fonction JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Sortie de la console dans les outils de développeur web du navigateur :

```console
Hello, Blazor!
```

Pour éviter une fuite de mémoire et permettre `DotNetObjectReference`la collecte des ordures sur un composant qui crée un , adopter l’une des approches suivantes:

* Disposer de l’objet dans `DotNetObjectReference` la classe qui a créé l’instance:

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

  Le modèle précédent `ExampleJsInterop` indiqué dans la classe peut également être mis en œuvre dans un composant :

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

* Lorsque le composant ou la classe `DotNetObjectReference`ne dispose pas de l’objet, disposer de l’objet sur le client en appelant `.dispose()`:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Appel de méthode d’instance de composant

Pour invoquer les méthodes .NET d’un composant :

* Utilisez `invokeMethod` le `invokeMethodAsync` ou la fonction pour faire un appel de méthode statique au composant.
* La méthode statique du composant enveloppe l’appel à `Action`sa méthode d’instance comme une invoquée .

Dans le JavaScript côté client :

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Pages/JSInteropComponent.razor*:

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

Lorsqu’il y a plusieurs composants, chacun avec des méthodes d’instance à `Action`appeler, utilisez une classe d’aide pour invoquer les méthodes d’instance (comme s) de chaque composant.

Dans l’exemple suivant :

* Le `JSInterop` composant `ListItem` contient plusieurs composants.
* Chaque `ListItem` composant est composé d’un message et d’un bouton.
* Lorsqu’un `ListItem` bouton composant `ListItem`est `UpdateMessage` sélectionné, cette méthode modifie le texte de l’élément de liste et cache le bouton.

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

Dans le JavaScript côté client :

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Partagé/ListItem.razor*:

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

*Pages/JSInterop.razor*:

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

## <a name="avoid-circular-object-references"></a>Évitez les références circulaires d’objets

Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour l’un ou l’autre:

* appels de méthode .NET.
* La méthode JavaScript appelle à partir de C lorsque le type de retour a des références circulaires.

Pour plus d’informations, voir les questions suivantes :

* [Les références circulaires ne sont pas prises en charge, en prennent deux (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Proposition : Ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/call-javascript-from-dotnet>
* [InteropComponent.razor exemple (dotnet/AspNetCore GitHub référentiel, branche de sortie 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Effectuer de grands Blazor transferts de données dans les applications Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
