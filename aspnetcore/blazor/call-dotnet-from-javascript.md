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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="8d5e0-103">Appelez les méthodes .NET des fonctions JavaScript dans ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="8d5e0-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="8d5e0-104">Par [Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8d5e0-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8d5e0-105">Une Blazor application peut invoquer les fonctions JavaScript à partir de méthodes .NET et de méthodes .NET à partir des fonctions JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="8d5e0-106">Ces scénarios sont appelés *Interopability JavaScript* (*JS interop*).</span><span class="sxs-lookup"><span data-stu-id="8d5e0-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="8d5e0-107">Cet article couvre l’invocation des méthodes .NET de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="8d5e0-108">Pour plus d’informations sur la façon d’appeler les fonctions JavaScript à partir de .NET, voir <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="8d5e0-109">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8d5e0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="8d5e0-110">Appel statique de méthode .NET</span><span class="sxs-lookup"><span data-stu-id="8d5e0-110">Static .NET method call</span></span>

<span data-ttu-id="8d5e0-111">Pour invoquer une méthode statique .NET `DotNet.invokeMethodAsync` de JavaScript, utilisez le ou les `DotNet.invokeMethod` fonctions.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="8d5e0-112">Passez dans l’identifiant de la méthode statique que vous souhaitez appeler, le nom de l’assemblage contenant la fonction, et tous les arguments.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="8d5e0-113">La version asynchrone est Blazor préférée pour prendre en charge les scénarios Server.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="8d5e0-114">La méthode .NET doit être publique, `[JSInvokable]` statique, et avoir l’attribut.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="8d5e0-115">Appeler des méthodes génériques ouvertes n’est pas actuellement pris en charge.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="8d5e0-116">L’application d’échantillon inclut une `int` méthode de C pour retourner un tableau.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="8d5e0-117">L’attribut `JSInvokable` est appliqué à la méthode.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="8d5e0-118">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="8d5e0-119">JavaScript servi au client invoque la méthode C .NET.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="8d5e0-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="8d5e0-121">Lorsque la **méthode statique Trigger .NET ReturnArrayAsync** bouton est sélectionné, examiner la sortie de la console dans les outils de développeur web du navigateur.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="8d5e0-122">La sortie de la console est :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="8d5e0-123">La valeur du quatrième tableau`data.push(4);`est poussée `ReturnArrayAsync`à la gamme ( ) retourné par .</span><span class="sxs-lookup"><span data-stu-id="8d5e0-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="8d5e0-124">Par défaut, l’identifiant de méthode est le nom de `JSInvokableAttribute` la méthode, mais vous pouvez spécifier un identifiant différent à l’aide du constructeur :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="8d5e0-125">Dans le fichier JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="8d5e0-126">Appel de méthode d’instance</span><span class="sxs-lookup"><span data-stu-id="8d5e0-126">Instance method call</span></span>

<span data-ttu-id="8d5e0-127">Vous pouvez également appeler des méthodes d’instance .NET de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="8d5e0-128">Pour invoquer une méthode d’instance .NET de JavaScript :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="8d5e0-129">Passez l’instance .NET par référence à JavaScript:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="8d5e0-130">Faites un appel `DotNetObjectReference.Create`statique à .</span><span class="sxs-lookup"><span data-stu-id="8d5e0-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="8d5e0-131">Enveloppez l’instance dans un `DotNetObjectReference` cas et faites appel `Create` à l’instance. `DotNetObjectReference`</span><span class="sxs-lookup"><span data-stu-id="8d5e0-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="8d5e0-132">Disposer `DotNetObjectReference` d’objets (un exemple apparaît plus tard dans cette section).</span><span class="sxs-lookup"><span data-stu-id="8d5e0-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="8d5e0-133">Invoquez des méthodes d’instance .NET sur l’instance à l’aide de la ou `invokeMethod` `invokeMethodAsync` des fonctions.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="8d5e0-134">L’instance .NET peut également être adoptée comme un argument lors de l’invocation d’autres méthodes .NET de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="8d5e0-135">L’application échantillon enregistre des messages à la console côté client.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="8d5e0-136">Pour les exemples suivants démontrés par l’application d’échantillon, examinez la sortie de la console du navigateur dans les outils de développeur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="8d5e0-137">Lorsque la **méthode d’instance Trigger .NET HelloHelper.SayHello** bouton est sélectionné, `ExampleJsInterop.CallHelloHelperSayHello` est appelé et passe un nom, `Blazor`, à la méthode.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="8d5e0-138">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="8d5e0-139">`CallHelloHelperSayHello`invoque la `sayHello` fonction JavaScript `HelloHelper`avec une nouvelle instance de .</span><span class="sxs-lookup"><span data-stu-id="8d5e0-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="8d5e0-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="8d5e0-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="8d5e0-142">Le nom est `HelloHelper`transmis au constructeur de `HelloHelper.Name` 's, qui définit la propriété.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="8d5e0-143">Lorsque la fonction `sayHello` JavaScript `HelloHelper.SayHello` est `Hello, {Name}!` exécutée, renvoie le message, qui est écrit sur la console par la fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="8d5e0-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="8d5e0-145">Sortie de la console dans les outils de développeur web du navigateur :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="8d5e0-146">Pour éviter une fuite de mémoire et permettre `DotNetObjectReference`la collecte des ordures sur un composant qui crée un , adopter l’une des approches suivantes:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="8d5e0-147">Disposer de l’objet dans `DotNetObjectReference` la classe qui a créé l’instance:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="8d5e0-148">Le modèle précédent `ExampleJsInterop` indiqué dans la classe peut également être mis en œuvre dans un composant :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="8d5e0-149">Lorsque le composant ou la classe `DotNetObjectReference`ne dispose pas de l’objet, disposer de l’objet sur le client en appelant `.dispose()`:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="8d5e0-150">Appel de méthode d’instance de composant</span><span class="sxs-lookup"><span data-stu-id="8d5e0-150">Component instance method call</span></span>

<span data-ttu-id="8d5e0-151">Pour invoquer les méthodes .NET d’un composant :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="8d5e0-152">Utilisez `invokeMethod` le `invokeMethodAsync` ou la fonction pour faire un appel de méthode statique au composant.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="8d5e0-153">La méthode statique du composant enveloppe l’appel à `Action`sa méthode d’instance comme une invoquée .</span><span class="sxs-lookup"><span data-stu-id="8d5e0-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="8d5e0-154">Dans le JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="8d5e0-155">*Pages/JSInteropComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="8d5e0-156">Lorsqu’il y a plusieurs composants, chacun avec des méthodes d’instance à `Action`appeler, utilisez une classe d’aide pour invoquer les méthodes d’instance (comme s) de chaque composant.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="8d5e0-157">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-157">In the following example:</span></span>

* <span data-ttu-id="8d5e0-158">Le `JSInterop` composant `ListItem` contient plusieurs composants.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="8d5e0-159">Chaque `ListItem` composant est composé d’un message et d’un bouton.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="8d5e0-160">Lorsqu’un `ListItem` bouton composant `ListItem`est `UpdateMessage` sélectionné, cette méthode modifie le texte de l’élément de liste et cache le bouton.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="8d5e0-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="8d5e0-162">Dans le JavaScript côté client :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="8d5e0-163">*Partagé/ListItem.razor*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="8d5e0-164">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="8d5e0-165">Évitez les références circulaires d’objets</span><span class="sxs-lookup"><span data-stu-id="8d5e0-165">Avoid circular object references</span></span>

<span data-ttu-id="8d5e0-166">Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour l’un ou l’autre:</span><span class="sxs-lookup"><span data-stu-id="8d5e0-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="8d5e0-167">appels de méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-167">.NET method calls.</span></span>
* <span data-ttu-id="8d5e0-168">La méthode JavaScript appelle à partir de C lorsque le type de retour a des références circulaires.</span><span class="sxs-lookup"><span data-stu-id="8d5e0-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="8d5e0-169">Pour plus d’informations, voir les questions suivantes :</span><span class="sxs-lookup"><span data-stu-id="8d5e0-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="8d5e0-170">Les références circulaires ne sont pas prises en charge, en prennent deux (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="8d5e0-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="8d5e0-171">Proposition : Ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="8d5e0-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="8d5e0-172">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8d5e0-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="8d5e0-173">InteropComponent.razor exemple (dotnet/AspNetCore GitHub référentiel, branche de sortie 3.1)</span><span class="sxs-lookup"><span data-stu-id="8d5e0-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="8d5e0-174">[Effectuer de grands Blazor transferts de données dans les applications Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="8d5e0-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
