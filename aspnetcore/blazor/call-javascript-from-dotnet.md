---
title: Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET Core Blazor
author: guardrex
description: Découvrez comment appeler des fonctions JavaScript à partir de méthodes .NET dans des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: a62462e3a0a2366a8662573ada5d2e7589c14c0d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722473"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="d5508-103">Appeler des fonctions JavaScript à partir de méthodes .NET dans ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d5508-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="d5508-104">Par [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d5508-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d5508-105">Une Blazor application peut appeler des fonctions JavaScript à partir de méthodes .net et de méthodes .net à partir de fonctions JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5508-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="d5508-106">Ces scénarios portent le nom de *l’interopérabilité de JavaScript* (*js Interop*).</span><span class="sxs-lookup"><span data-stu-id="d5508-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="d5508-107">Cet article traite de l’appel de fonctions JavaScript à partir de .NET.</span><span class="sxs-lookup"><span data-stu-id="d5508-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="d5508-108">Pour plus d’informations sur la façon d’appeler des méthodes .NET à partir de JavaScript, consultez <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="d5508-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="d5508-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5508-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d5508-110">Pour appeler JavaScript à partir de .NET, utilisez l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span><span class="sxs-lookup"><span data-stu-id="d5508-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="d5508-111">Pour émettre des appels d’interopérabilité JS, injectez l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans votre composant.</span><span class="sxs-lookup"><span data-stu-id="d5508-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="d5508-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> prend un identificateur pour la fonction JavaScript que vous souhaitez appeler, ainsi que n’importe quel nombre d’arguments sérialisables JSON.</span><span class="sxs-lookup"><span data-stu-id="d5508-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="d5508-113">L’identificateur de fonction est relatif à la portée globale ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="d5508-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="d5508-114">Si vous souhaitez appeler `window.someScope.someFunction` , l’identificateur est `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="d5508-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="d5508-115">Il n’est pas nécessaire d’inscrire la fonction avant qu’elle ne soit appelée.</span><span class="sxs-lookup"><span data-stu-id="d5508-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="d5508-116">Le type de retour `T` doit également être sérialisable JSON.</span><span class="sxs-lookup"><span data-stu-id="d5508-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="d5508-117">`T` doit correspondre au type .NET qui correspond le mieux au type JSON retourné.</span><span class="sxs-lookup"><span data-stu-id="d5508-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="d5508-118">Pour Blazor Server les applications pour lesquelles le prérendu est activé, l’appel à JavaScript n’est pas possible pendant le prérendu initial.</span><span class="sxs-lookup"><span data-stu-id="d5508-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="d5508-119">Les appels Interop JavaScript doivent être différés jusqu’à ce que la connexion avec le navigateur soit établie.</span><span class="sxs-lookup"><span data-stu-id="d5508-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="d5508-120">Pour plus d’informations, consultez la section [détecter le Blazor Server prérendu d’une application](#detect-when-a-blazor-server-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="d5508-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="d5508-121">L’exemple suivant est basé sur [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) , un décodeur basé sur JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5508-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="d5508-122">L’exemple montre comment appeler une fonction JavaScript à partir d’une méthode C# qui décharge une spécification du code de développeur vers une API JavaScript existante.</span><span class="sxs-lookup"><span data-stu-id="d5508-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="d5508-123">La fonction JavaScript accepte un tableau d’octets d’une méthode C#, décode le tableau et retourne le texte au composant pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="d5508-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="d5508-124">À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une fonction JavaScript qui utilise `TextDecoder` pour décoder un tableau passé et retourner la valeur décodée :</span><span class="sxs-lookup"><span data-stu-id="d5508-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="d5508-125">Du code JavaScript, tel que le code illustré dans l’exemple précédent, peut également être chargé à partir d’un fichier JavaScript ( `.js` ) avec une référence au fichier de script :</span><span class="sxs-lookup"><span data-stu-id="d5508-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="d5508-126">Le composant suivant :</span><span class="sxs-lookup"><span data-stu-id="d5508-126">The following component:</span></span>

* <span data-ttu-id="d5508-127">Appelle la `convertArray` fonction JavaScript à l’aide `JSRuntime` de lorsqu’un bouton de composant ( **`Convert Array`** ) est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="d5508-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="d5508-128">Une fois la fonction JavaScript appelée, le tableau passé est converti en chaîne.</span><span class="sxs-lookup"><span data-stu-id="d5508-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="d5508-129">La chaîne est retournée au composant pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="d5508-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="d5508-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="d5508-130">IJSRuntime</span></span>

<span data-ttu-id="d5508-131">Pour utiliser l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d5508-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="d5508-132">Injecter l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans le Razor composant ( `.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="d5508-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="d5508-133">À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une `handleTickerChanged` fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5508-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d5508-134">La fonction est appelée avec <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> et ne retourne pas de valeur :</span><span class="sxs-lookup"><span data-stu-id="d5508-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="d5508-135">Injecter l' <xref:Microsoft.JSInterop.IJSRuntime> abstraction dans une classe ( `.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="d5508-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="d5508-136">À l’intérieur `<head>` de l’élément de `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` ( Blazor Server ), fournissez une `handleTickerChanged` fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5508-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d5508-137">La fonction est appelée avec `JSRuntime.InvokeAsync` et retourne une valeur :</span><span class="sxs-lookup"><span data-stu-id="d5508-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="d5508-138">Pour la génération de contenu dynamique avec [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), utilisez l' `[Inject]` attribut :</span><span class="sxs-lookup"><span data-stu-id="d5508-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="d5508-139">Dans l’exemple d’application côté client qui accompagne cette rubrique, deux fonctions JavaScript sont disponibles pour l’application qui interagit avec le DOM pour recevoir l’entrée d’utilisateur et afficher un message d’accueil :</span><span class="sxs-lookup"><span data-stu-id="d5508-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="d5508-140">`showPrompt`: Génère une invite pour accepter l’entrée d’utilisateur (nom de l’utilisateur) et retourne le nom à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="d5508-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="d5508-141">`displayWelcome`: Assigne un message de bienvenue de l’appelant à un objet DOM avec un `id` de `welcome` .</span><span class="sxs-lookup"><span data-stu-id="d5508-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="d5508-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d5508-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="d5508-143">Placez la `<script>` balise qui référence le fichier JavaScript dans le fichier `wwwroot/index.html` ( Blazor WebAssembly ) ou le `Pages/_Host.cshtml` fichier ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="d5508-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="d5508-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="d5508-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="d5508-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="d5508-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="d5508-146">Ne placez pas de `<script>` balise dans un fichier de composant parce que la `<script>` balise ne peut pas être mise à jour dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="d5508-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="d5508-147">Les méthodes .NET interagissent avec les fonctions JavaScript dans le `exampleJsInterop.js` fichier en appelant <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d5508-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="d5508-148">L' <xref:Microsoft.JSInterop.IJSRuntime> abstraction est asynchrone pour permettre les Blazor Server scénarios.</span><span class="sxs-lookup"><span data-stu-id="d5508-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="d5508-149">Si l’application est une Blazor WebAssembly application et que vous souhaitez appeler une fonction JavaScript de manière synchrone, vous devez effectuer un cast aval vers <xref:Microsoft.JSInterop.IJSInProcessRuntime> et appeler à la <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> place.</span><span class="sxs-lookup"><span data-stu-id="d5508-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="d5508-150">Nous recommandons que la plupart des bibliothèques d’interopérabilité JS utilisent les API Async pour s’assurer que les bibliothèques sont disponibles dans tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="d5508-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d5508-151">Pour activer l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard, consultez la section [ Blazor isolation JavaScript et références d’objets](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="d5508-151">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="d5508-152">L’exemple d’application comprend un composant pour illustrer l’interopérabilité de JS.</span><span class="sxs-lookup"><span data-stu-id="d5508-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="d5508-153">Le composant :</span><span class="sxs-lookup"><span data-stu-id="d5508-153">The component:</span></span>

* <span data-ttu-id="d5508-154">Reçoit une entrée d’utilisateur via une invite JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5508-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="d5508-155">Retourne le texte du composant pour traitement.</span><span class="sxs-lookup"><span data-stu-id="d5508-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="d5508-156">Appelle une deuxième fonction JavaScript qui interagit avec le DOM pour afficher un message d’accueil.</span><span class="sxs-lookup"><span data-stu-id="d5508-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="d5508-157">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="d5508-157">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
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

<span data-ttu-id="d5508-158">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d5508-158">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="d5508-159">Lorsque `TriggerJsPrompt` est exécuté en sélectionnant le bouton du composant **`Trigger JavaScript Prompt`** , la `showPrompt` fonction JavaScript fournie dans le `wwwroot/exampleJsInterop.js` fichier est appelée.</span><span class="sxs-lookup"><span data-stu-id="d5508-159">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="d5508-160">La `showPrompt` fonction accepte l’entrée d’utilisateur (nom de l’utilisateur), qui est codée au format HTML et renvoyée au composant.</span><span class="sxs-lookup"><span data-stu-id="d5508-160">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="d5508-161">Le composant stocke le nom de l’utilisateur dans une variable locale, `name` .</span><span class="sxs-lookup"><span data-stu-id="d5508-161">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="d5508-162">La chaîne stockée dans `name` est incorporée dans un message de bienvenue, qui est transmis à une fonction JavaScript, `displayWelcome` , qui affiche le message de bienvenue dans une balise de titre.</span><span class="sxs-lookup"><span data-stu-id="d5508-162">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="d5508-163">Appeler une fonction JavaScript void</span><span class="sxs-lookup"><span data-stu-id="d5508-163">Call a void JavaScript function</span></span>

<span data-ttu-id="d5508-164">Les fonctions JavaScript qui retournent [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) sont appelées avec <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d5508-164">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="d5508-165">Détecter quand une Blazor Server application est prérendu</span><span class="sxs-lookup"><span data-stu-id="d5508-165">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="d5508-166">Capturer des références à des éléments</span><span class="sxs-lookup"><span data-stu-id="d5508-166">Capture references to elements</span></span>

<span data-ttu-id="d5508-167">Certains scénarios d’interopérabilité JS requièrent des références à des éléments HTML.</span><span class="sxs-lookup"><span data-stu-id="d5508-167">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="d5508-168">Par exemple, une bibliothèque d’interface utilisateur peut nécessiter une référence d’élément pour l’initialisation, ou vous devrez peut-être appeler des API de type commande sur un élément, tel que `focus` ou `play` .</span><span class="sxs-lookup"><span data-stu-id="d5508-168">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="d5508-169">Capturer des références aux éléments HTML dans un composant à l’aide de l’approche suivante :</span><span class="sxs-lookup"><span data-stu-id="d5508-169">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="d5508-170">Ajoutez un `@ref` attribut à l’élément HTML.</span><span class="sxs-lookup"><span data-stu-id="d5508-170">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="d5508-171">Définissez un champ de type <xref:Microsoft.AspNetCore.Components.ElementReference> dont le nom correspond à la valeur de l' `@ref` attribut.</span><span class="sxs-lookup"><span data-stu-id="d5508-171">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="d5508-172">L’exemple suivant illustre la capture d’une référence à l' `username` `<input>` élément :</span><span class="sxs-lookup"><span data-stu-id="d5508-172">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="d5508-173">Utilisez uniquement une référence d’élément pour muter le contenu d’un élément vide qui n’interagit pas avec Blazor .</span><span class="sxs-lookup"><span data-stu-id="d5508-173">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="d5508-174">Ce scénario est utile quand une API tierce fournit du contenu à l’élément.</span><span class="sxs-lookup"><span data-stu-id="d5508-174">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="d5508-175">Étant donné que Blazor n’interagit pas avec l’élément, il n’existe aucun risque de conflit entre Blazor la représentation de l’élément et le DOM.</span><span class="sxs-lookup"><span data-stu-id="d5508-175">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="d5508-176">Dans l’exemple suivant, il est *dangereux* de faire muter le contenu de la liste non triée ( `ul` ), car Blazor interagit avec le DOM pour remplir les éléments de liste de cet élément ( `<li>` ) :</span><span class="sxs-lookup"><span data-stu-id="d5508-176">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="d5508-177">Si l’interopérabilité JS muter le contenu de l’élément `MyList` et Blazor tente d’appliquer des différences à l’élément, les différences ne correspondent pas au DOM.</span><span class="sxs-lookup"><span data-stu-id="d5508-177">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="d5508-178">En ce qui concerne le code .NET, un <xref:Microsoft.AspNetCore.Components.ElementReference> est un handle opaque.</span><span class="sxs-lookup"><span data-stu-id="d5508-178">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="d5508-179">La *seule* chose que vous pouvez faire avec <xref:Microsoft.AspNetCore.Components.ElementReference> est de la passer au code JavaScript via l’interopérabilité de js.</span><span class="sxs-lookup"><span data-stu-id="d5508-179">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="d5508-180">Dans ce cas, le code JavaScript reçoit une `HTMLElement` instance, qu’il peut utiliser avec les API DOM normales.</span><span class="sxs-lookup"><span data-stu-id="d5508-180">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="d5508-181">Par exemple, le code suivant définit une méthode d’extension .NET qui permet de définir le focus sur un élément :</span><span class="sxs-lookup"><span data-stu-id="d5508-181">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="d5508-182">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="d5508-182">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="d5508-183">Pour appeler une fonction JavaScript qui ne retourne pas de valeur, utilisez <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d5508-183">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d5508-184">Le code suivant définit le focus sur l’entrée de nom d’utilisateur en appelant la fonction JavaScript précédente avec le capturé <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="d5508-184">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="d5508-185">Pour utiliser une méthode d’extension, créez une méthode d’extension statique qui reçoit l' <xref:Microsoft.JSInterop.IJSRuntime> instance :</span><span class="sxs-lookup"><span data-stu-id="d5508-185">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="d5508-186">La `Focus` méthode est appelée directement sur l’objet.</span><span class="sxs-lookup"><span data-stu-id="d5508-186">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="d5508-187">L’exemple suivant suppose que la `Focus` méthode est disponible à partir de l' `JsInteropClasses` espace de noms :</span><span class="sxs-lookup"><span data-stu-id="d5508-187">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="d5508-188">La `username` variable est remplie uniquement après le rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="d5508-188">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="d5508-189">Si un non rempli <xref:Microsoft.AspNetCore.Components.ElementReference> est passé à du code JavaScript, le code JavaScript reçoit la valeur `null` .</span><span class="sxs-lookup"><span data-stu-id="d5508-189">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="d5508-190">Pour manipuler des références d’élément une fois que le composant a terminé le rendu (pour définir le focus initial sur un élément), utilisez les [ `OnAfterRenderAsync` méthodes de cycle de vie des `OnAfterRender` composants ou](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="d5508-190">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="d5508-191">Lorsque vous utilisez des types génériques et que vous retournez une valeur, utilisez <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="d5508-191">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="d5508-192">`GenericMethod` est appelé directement sur l’objet avec un type.</span><span class="sxs-lookup"><span data-stu-id="d5508-192">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="d5508-193">L’exemple suivant suppose que le `GenericMethod` est disponible à partir de l' `JsInteropClasses` espace de noms :</span><span class="sxs-lookup"><span data-stu-id="d5508-193">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="d5508-194">Éléments de référence sur les composants</span><span class="sxs-lookup"><span data-stu-id="d5508-194">Reference elements across components</span></span>

<span data-ttu-id="d5508-195">Un <xref:Microsoft.AspNetCore.Components.ElementReference> est garanti uniquement valide dans la méthode d’un composant <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> (et une référence d’élément est un `struct` ), de sorte qu’une référence d’élément ne peut pas être transmise entre les composants.</span><span class="sxs-lookup"><span data-stu-id="d5508-195">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="d5508-196">Pour qu’un composant parent rende une référence d’élément disponible pour d’autres composants, le composant parent peut :</span><span class="sxs-lookup"><span data-stu-id="d5508-196">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="d5508-197">Autorisez les composants enfants à inscrire des rappels.</span><span class="sxs-lookup"><span data-stu-id="d5508-197">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="d5508-198">Appelez les rappels inscrits pendant l' <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> événement avec la référence d’élément passée.</span><span class="sxs-lookup"><span data-stu-id="d5508-198">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="d5508-199">Indirectement, cette approche permet aux composants enfants d’interagir avec la référence d’élément du parent.</span><span class="sxs-lookup"><span data-stu-id="d5508-199">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="d5508-200">L' Blazor WebAssembly exemple suivant illustre l’approche.</span><span class="sxs-lookup"><span data-stu-id="d5508-200">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="d5508-201">Dans le `<head>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="d5508-201">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="d5508-202">Dans le `<body>` de `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="d5508-202">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="d5508-203">`Pages/Index.razor` (composant parent) :</span><span class="sxs-lookup"><span data-stu-id="d5508-203">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="d5508-204">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5508-204">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
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

<span data-ttu-id="d5508-205">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d5508-205">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="d5508-206">`Shared/SurveyPrompt.razor` (composant enfant) :</span><span class="sxs-lookup"><span data-stu-id="d5508-206">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="d5508-207">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5508-207">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
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

<span data-ttu-id="d5508-208">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly d’application de l’application (par exemple, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="d5508-208">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="d5508-209">Sécuriser les appels d’interopérabilité JS</span><span class="sxs-lookup"><span data-stu-id="d5508-209">Harden JS interop calls</span></span>

<span data-ttu-id="d5508-210">L’interopérabilité de JS peut échouer en raison d’erreurs réseau et doit être considérée comme non fiable.</span><span class="sxs-lookup"><span data-stu-id="d5508-210">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="d5508-211">Par défaut, une Blazor Server application expire les appels d’interopérabilité js sur le serveur après une minute.</span><span class="sxs-lookup"><span data-stu-id="d5508-211">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="d5508-212">Si une application peut tolérer un délai d’expiration plus agressif, définissez le délai d’expiration à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d5508-212">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="d5508-213">Globalement dans `Startup.ConfigureServices` , spécifiez le délai d’expiration :</span><span class="sxs-lookup"><span data-stu-id="d5508-213">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="d5508-214">Par appel dans le code du composant, un appel unique peut spécifier le délai d’attente :</span><span class="sxs-lookup"><span data-stu-id="d5508-214">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="d5508-215">Pour plus d’informations sur l’épuisement des ressources, consultez <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="d5508-215">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="d5508-216">Éviter les références d’objets circulaires</span><span class="sxs-lookup"><span data-stu-id="d5508-216">Avoid circular object references</span></span>

<span data-ttu-id="d5508-217">Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour :</span><span class="sxs-lookup"><span data-stu-id="d5508-217">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="d5508-218">Appels de méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="d5508-218">.NET method calls.</span></span>
* <span data-ttu-id="d5508-219">Appels de méthode JavaScript à partir de C# lorsque le type de retour a des références circulaires.</span><span class="sxs-lookup"><span data-stu-id="d5508-219">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="d5508-220">Pour plus d’informations, consultez les problèmes suivants :</span><span class="sxs-lookup"><span data-stu-id="d5508-220">For more information, see the following issues:</span></span>

* [<span data-ttu-id="d5508-221">Les références circulaires ne sont pas prises en charge, prennent deux (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="d5508-221">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="d5508-222">Proposition : ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/Runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="d5508-222">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="d5508-223">Blazor Isolation JavaScript et références d’objets</span><span class="sxs-lookup"><span data-stu-id="d5508-223">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="d5508-224">Blazor active l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard.</span><span class="sxs-lookup"><span data-stu-id="d5508-224">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="d5508-225">L’isolation JavaScript offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="d5508-225">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="d5508-226">Le JavaScript importé ne pollue plus l’espace de noms global.</span><span class="sxs-lookup"><span data-stu-id="d5508-226">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="d5508-227">Les consommateurs d’une bibliothèque et de composants ne sont pas requis pour importer le code JavaScript associé.</span><span class="sxs-lookup"><span data-stu-id="d5508-227">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="d5508-228">Par exemple, le module JavaScript suivant exporte une fonction JavaScript pour afficher une invite de navigateur :</span><span class="sxs-lookup"><span data-stu-id="d5508-228">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="d5508-229">Ajoutez le module JavaScript précédent à une bibliothèque .NET en tant que ressource Web statique ( `wwwroot/exampleJsInterop.js` ), puis importez le module dans le code .net à l’aide du <xref:Microsoft.JSInterop.IJSRuntime> service.</span><span class="sxs-lookup"><span data-stu-id="d5508-229">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="d5508-230">Le service est injecté comme `jsRuntime` (non affiché) pour l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d5508-230">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="d5508-231">L' `import` identificateur dans l’exemple précédent est un identificateur spécial utilisé spécifiquement pour l’importation d’un module JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5508-231">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="d5508-232">Spécifiez le module à l’aide de son chemin d’accès aux ressources Web statiques stable : `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="d5508-232">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="d5508-233">L’espace réservé `{LIBRARY NAME}` est le nom de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="d5508-233">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="d5508-234">L’espace réservé `{PATH UNDER WWWROOT}` est le chemin d’accès au script sous `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="d5508-234">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="d5508-235"><xref:Microsoft.JSInterop.IJSRuntime> importe le module en tant que `JSObjectReference` , qui représente une référence à un objet JavaScript à partir du code .net.</span><span class="sxs-lookup"><span data-stu-id="d5508-235"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="d5508-236">Utilisez `JSObjectReference` pour appeler les fonctions JavaScript exportées à partir du module :</span><span class="sxs-lookup"><span data-stu-id="d5508-236">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d5508-237">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d5508-237">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="d5508-238">Exemple InteropComponent. Razor (référentiel GitHub dotnet/AspNetCore, branche de version 3,1)</span><span class="sxs-lookup"><span data-stu-id="d5508-238">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="d5508-239">Effectuer des transferts de données volumineux dans des Blazor Server applications</span><span class="sxs-lookup"><span data-stu-id="d5508-239">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
