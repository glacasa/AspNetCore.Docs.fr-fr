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
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="72e33-103">Appelez les fonctions JavaScript à partir de méthodes .NET dans ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="72e33-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="72e33-104">Par [Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="72e33-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="72e33-105">Une Blazor application peut invoquer les fonctions JavaScript à partir de méthodes .NET et de méthodes .NET à partir des fonctions JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72e33-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="72e33-106">Ces scénarios sont appelés *Interopability JavaScript* (*JS interop*).</span><span class="sxs-lookup"><span data-stu-id="72e33-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="72e33-107">Cet article couvre l’invocation des fonctions JavaScript de .NET.</span><span class="sxs-lookup"><span data-stu-id="72e33-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="72e33-108">Pour plus d’informations sur la façon <xref:blazor/call-dotnet-from-javascript>d’appeler les méthodes .NET de JavaScript, voir .</span><span class="sxs-lookup"><span data-stu-id="72e33-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="72e33-109">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72e33-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="72e33-110">Pour appeler JavaScript à partir `IJSRuntime` de .NET, utilisez l’abstraction.</span><span class="sxs-lookup"><span data-stu-id="72e33-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="72e33-111">Pour émettre des appels interop JS, injectez l’abstraction `IJSRuntime` dans votre composant.</span><span class="sxs-lookup"><span data-stu-id="72e33-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="72e33-112">La `InvokeAsync<T>` méthode prend un identifiant pour la fonction JavaScript que vous souhaitez invoquer avec un certain nombre d’arguments JSON-sérialisable.</span><span class="sxs-lookup"><span data-stu-id="72e33-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="72e33-113">L’identifiant de fonction est`window`relatif à la portée globale ().</span><span class="sxs-lookup"><span data-stu-id="72e33-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="72e33-114">Si vous souhaitez `window.someScope.someFunction`appeler, `someScope.someFunction`l’identifiant est .</span><span class="sxs-lookup"><span data-stu-id="72e33-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="72e33-115">Il n’est pas nécessaire d’enregistrer la fonction avant qu’elle ne s’appelle.</span><span class="sxs-lookup"><span data-stu-id="72e33-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="72e33-116">Le type `T` de retour doit également être sérialisable JSON.</span><span class="sxs-lookup"><span data-stu-id="72e33-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="72e33-117">`T`devrait correspondre au type .NET que les meilleures cartes au type JSON retourné.</span><span class="sxs-lookup"><span data-stu-id="72e33-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="72e33-118">Pour Blazor les applications Server avec prerendering activé, appeler dans JavaScript n’est pas possible lors du prérendering initial.</span><span class="sxs-lookup"><span data-stu-id="72e33-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="72e33-119">Les appels interop JavaScript doivent être reportés jusqu’à ce que la connexion avec le navigateur soit établie.</span><span class="sxs-lookup"><span data-stu-id="72e33-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="72e33-120">Pour plus d’informations, voir la [détection Blazor lorsqu’une application Server est la section de préréaender.](#detect-when-a-blazor-server-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="72e33-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="72e33-121">L’exemple suivant est basé sur [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un décodeur basé sur JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72e33-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="72e33-122">L’exemple montre comment invoquer une fonction JavaScript à partir d’une méthode C.</span><span class="sxs-lookup"><span data-stu-id="72e33-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="72e33-123">La fonction JavaScript accepte un tableau d’ord d’une méthode C, décode le tableau et renvoie le texte au composant pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="72e33-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="72e33-124">À `<head>` l’intérieur deBlazor l’élément de *wwwroot/index.html* (WebAssembly) ou *Pages/_Host.cshtml* Blazor ( `TextDecoder` Server), fournir une fonction JavaScript qui utilise pour décoder un tableau passé et retourner la valeur décodée:</span><span class="sxs-lookup"><span data-stu-id="72e33-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="72e33-125">Le code JavaScript, tel que le code indiqué dans l’exemple précédent, peut également être chargé à partir d’un fichier JavaScript (*.js*) avec une référence au fichier script :</span><span class="sxs-lookup"><span data-stu-id="72e33-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="72e33-126">Le composant suivant :</span><span class="sxs-lookup"><span data-stu-id="72e33-126">The following component:</span></span>

* <span data-ttu-id="72e33-127">Invoque `convertArray` la fonction `JSRuntime` JavaScript à l’aide de la sélection d’un bouton composant (**Convert Array).**</span><span class="sxs-lookup"><span data-stu-id="72e33-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="72e33-128">Après que la fonction JavaScript est appelée, le tableau passé est converti en une chaîne.</span><span class="sxs-lookup"><span data-stu-id="72e33-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="72e33-129">La chaîne est retournée au composant pour l’affichage.</span><span class="sxs-lookup"><span data-stu-id="72e33-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="72e33-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="72e33-130">IJSRuntime</span></span>

<span data-ttu-id="72e33-131">Pour utiliser `IJSRuntime` l’abstraction, adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="72e33-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="72e33-132">Injecter `IJSRuntime` l’abstraction dans le composant Razor (*.razor*):</span><span class="sxs-lookup"><span data-stu-id="72e33-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="72e33-133">À `<head>` l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly)Blazor ou *Pages/_Host.cshtml* (Blazor Server), fournir une `handleTickerChanged` fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72e33-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="72e33-134">La fonction est `IJSRuntime.InvokeVoidAsync` appelée avec et ne retourne pas une valeur:</span><span class="sxs-lookup"><span data-stu-id="72e33-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="72e33-135">Injecter `IJSRuntime` l’abstraction dans une classe (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="72e33-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="72e33-136">À `<head>` l’intérieur de l’élément de *wwwroot/index.html* (WebAssembly)Blazor ou *Pages/_Host.cshtml* (Blazor Server), fournir une `handleTickerChanged` fonction JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72e33-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="72e33-137">La fonction est `JSRuntime.InvokeAsync` appelée avec et retourne une valeur:</span><span class="sxs-lookup"><span data-stu-id="72e33-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="72e33-138">Pour la génération de contenu dynamique `[Inject]` avec [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), utilisez l’attribut :</span><span class="sxs-lookup"><span data-stu-id="72e33-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="72e33-139">Dans l’application d’échantillon côté client qui accompagne ce sujet, deux fonctions JavaScript sont disponibles pour l’application qui interagissent avec le DOM pour recevoir l’entrée de l’utilisateur et afficher un message de bienvenue :</span><span class="sxs-lookup"><span data-stu-id="72e33-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="72e33-140">`showPrompt`&ndash; Produit une invite à accepter l’entrée de l’utilisateur (nom de l’utilisateur) et renvoie le nom à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="72e33-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="72e33-141">`displayWelcome`&ndash; Assigne un message de bienvenue de l’appelant à un objet DOM avec un `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="72e33-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="72e33-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="72e33-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="72e33-143">Placez `<script>` l’étiquette qui fait référence au fichierBlazor JavaScript dans le fichier *wwwroot/index.html* (WebAssembly) ouBlazor *Pages/_Host.cshtml* fichier (Serveur).</span><span class="sxs-lookup"><span data-stu-id="72e33-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="72e33-144">*wwwroot/index.html* (WebAssembly):Blazor</span><span class="sxs-lookup"><span data-stu-id="72e33-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="72e33-145">*Pages/_Host.cshtml* (Serveur)Blazor :</span><span class="sxs-lookup"><span data-stu-id="72e33-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="72e33-146">Ne placez `<script>` pas une étiquette dans `<script>` un fichier de composant parce que l’étiquette ne peut pas être mise à jour dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="72e33-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="72e33-147">.NET méthodes interop avec les fonctions JavaScript dans le fichier `IJSRuntime.InvokeAsync<T>` *exampleJsInterop.js* en appelant .</span><span class="sxs-lookup"><span data-stu-id="72e33-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="72e33-148">L’abstraction `IJSRuntime` est asynchrone Blazor pour permettre des scénarios Server.</span><span class="sxs-lookup"><span data-stu-id="72e33-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="72e33-149">Si l’application Blazor est une application WebAssembly et que vous souhaitez invoquer `Invoke<T>` une fonction JavaScript de façon synchronisée, abattue et appeler à `IJSInProcessRuntime` la place.</span><span class="sxs-lookup"><span data-stu-id="72e33-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="72e33-150">Nous recommandons que la plupart des bibliothèques interop JS utilisent les API async pour s’assurer que les bibliothèques sont disponibles dans tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="72e33-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="72e33-151">L’application d’échantillon inclut un composant pour démontrer JS interop.</span><span class="sxs-lookup"><span data-stu-id="72e33-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="72e33-152">Le composant:</span><span class="sxs-lookup"><span data-stu-id="72e33-152">The component:</span></span>

* <span data-ttu-id="72e33-153">Reçoit l’entrée de l’utilisateur via une invite JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72e33-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="72e33-154">Retourne le texte au composant pour le traitement.</span><span class="sxs-lookup"><span data-stu-id="72e33-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="72e33-155">Appelle une deuxième fonction JavaScript qui interagit avec le DOM pour afficher un message de bienvenue.</span><span class="sxs-lookup"><span data-stu-id="72e33-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="72e33-156">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="72e33-156">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="72e33-157">Lorsqu’elle `TriggerJsPrompt` est exécutée en sélectionnant le bouton **Trigger JavaScript Prompt** du composant, la fonction JavaScript `showPrompt` fournie dans le fichier *wwwroot/exampleJsInterop.js* est appelée.</span><span class="sxs-lookup"><span data-stu-id="72e33-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="72e33-158">La `showPrompt` fonction accepte l’entrée de l’utilisateur (nom de l’utilisateur), qui est codée en HTML et retournée au composant.</span><span class="sxs-lookup"><span data-stu-id="72e33-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="72e33-159">Le composant stocke le nom de `name`l’utilisateur dans une variable locale, .</span><span class="sxs-lookup"><span data-stu-id="72e33-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="72e33-160">La chaîne `name` stockée dans est incorporée dans un message de `displayWelcome`bienvenue, qui est passé à une fonction JavaScript, , qui rend le message de bienvenue dans une balise de titre.</span><span class="sxs-lookup"><span data-stu-id="72e33-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="72e33-161">Appelez une fonction JavaScript vide</span><span class="sxs-lookup"><span data-stu-id="72e33-161">Call a void JavaScript function</span></span>

<span data-ttu-id="72e33-162">Les fonctions JavaScript qui retournent [vide(0)/vide 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [indéfinis](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) sont appelées avec `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="72e33-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="72e33-163">Détecter quand Blazor une application Server est prerendering</span><span class="sxs-lookup"><span data-stu-id="72e33-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="72e33-164">Capturez les références aux éléments</span><span class="sxs-lookup"><span data-stu-id="72e33-164">Capture references to elements</span></span>

<span data-ttu-id="72e33-165">Certains scénarios interop JS nécessitent des références à des éléments HTML.</span><span class="sxs-lookup"><span data-stu-id="72e33-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="72e33-166">Par exemple, une bibliothèque d’interface utilisateur peut nécessiter une référence d’élément pour l’initialisation, ou vous pourriez avoir besoin d’appeler des API de commande sur un élément, tel que `focus` ou `play`.</span><span class="sxs-lookup"><span data-stu-id="72e33-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="72e33-167">Capturez les références aux éléments HTML d’un composant à l’aide de l’approche suivante :</span><span class="sxs-lookup"><span data-stu-id="72e33-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="72e33-168">Ajoutez `@ref` un attribut à l’élément HTML.</span><span class="sxs-lookup"><span data-stu-id="72e33-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="72e33-169">Définissez un `ElementReference` champ de type dont `@ref` le nom correspond à la valeur de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="72e33-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="72e33-170">L’exemple suivant montre la `username` `<input>` capture d’une référence à l’élément :</span><span class="sxs-lookup"><span data-stu-id="72e33-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="72e33-171">Utilisez uniquement une référence d’élément pour muter le contenu Blazord’un élément vide qui n’interagit pas avec .</span><span class="sxs-lookup"><span data-stu-id="72e33-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="72e33-172">Ce scénario est utile lorsqu’une API de tiers fournit du contenu à l’élément.</span><span class="sxs-lookup"><span data-stu-id="72e33-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="72e33-173">Parce Blazor que n’interagit pas avec l’élément, Blazoril n’y a aucune possibilité d’un conflit entre la représentation de l’élément et le DOM.</span><span class="sxs-lookup"><span data-stu-id="72e33-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="72e33-174">Dans l’exemple suivant, il est *dangereux* de muter le`ul`contenu Blazor de la liste non ordonnée ( )`<li>`parce que les interagit avec le DOM pour remplir les éléments de liste de cet élément (</span><span class="sxs-lookup"><span data-stu-id="72e33-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="72e33-175">Si JS interop mute `MyList` le Blazor contenu de l’élément et tente d’appliquer des diffs à l’élément, les diffs ne correspondent pas au DOM.</span><span class="sxs-lookup"><span data-stu-id="72e33-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="72e33-176">En ce qui concerne le `ElementReference` code .NET, un est une poignée opaque.</span><span class="sxs-lookup"><span data-stu-id="72e33-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="72e33-177">La *seule* chose que `ElementReference` vous pouvez faire avec est de le passer à travers le code JavaScript via JS interop.</span><span class="sxs-lookup"><span data-stu-id="72e33-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="72e33-178">Lorsque vous le faites, le code `HTMLElement` Côté JavaScript reçoit une instance, qu’il peut utiliser avec des API DOM normales.</span><span class="sxs-lookup"><span data-stu-id="72e33-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="72e33-179">Par exemple, le code suivant définit une méthode d’extension .NET qui permet de mettre l’accent sur un élément :</span><span class="sxs-lookup"><span data-stu-id="72e33-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="72e33-180">*exempleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="72e33-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="72e33-181">Pour appeler une fonction JavaScript qui ne `IJSRuntime.InvokeVoidAsync`retourne pas une valeur, utilisez .</span><span class="sxs-lookup"><span data-stu-id="72e33-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="72e33-182">Le code suivant met l’accent sur l’entrée du nom `ElementReference`d’utilisateur en appelant la fonction JavaScript précédente avec les personnes capturées :</span><span class="sxs-lookup"><span data-stu-id="72e33-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="72e33-183">Pour utiliser une méthode d’extension, créez `IJSRuntime` une méthode d’extension statique qui reçoit l’instance :</span><span class="sxs-lookup"><span data-stu-id="72e33-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="72e33-184">La `Focus` méthode est appelée directement sur l’objet.</span><span class="sxs-lookup"><span data-stu-id="72e33-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="72e33-185">L’exemple suivant suppose `Focus` que la `JsInteropClasses` méthode est disponible à partir de l’espace nom:</span><span class="sxs-lookup"><span data-stu-id="72e33-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="72e33-186">La `username` variable n’est peuplée qu’après la remise du composant.</span><span class="sxs-lookup"><span data-stu-id="72e33-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="72e33-187">Si un nonpopulé `ElementReference` est passé au code JavaScript, `null`le code JavaScript reçoit une valeur de .</span><span class="sxs-lookup"><span data-stu-id="72e33-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="72e33-188">Pour manipuler les références d’éléments après que le composant a fini le rendu (pour définir l’accent initial sur un élément) utiliser les [méthodes de cycle de vie du composant OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="72e33-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="72e33-189">Lorsque vous travaillez avec des types génériques et retournez une valeur, utilisez [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="72e33-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="72e33-190">`GenericMethod`est appelé directement sur l’objet avec un type.</span><span class="sxs-lookup"><span data-stu-id="72e33-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="72e33-191">L’exemple suivant suppose `GenericMethod` que le `JsInteropClasses` est disponible à partir de l’espace nom:</span><span class="sxs-lookup"><span data-stu-id="72e33-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="72e33-192">Éléments de référence entre les composants</span><span class="sxs-lookup"><span data-stu-id="72e33-192">Reference elements across components</span></span>

<span data-ttu-id="72e33-193">Un `ElementReference` est seulement garanti valable `OnAfterRender` dans la méthode d’un composant (et une référence d’élément est un `struct`), ainsi une référence d’élément ne peut pas être passée entre les composants.</span><span class="sxs-lookup"><span data-stu-id="72e33-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="72e33-194">Pour qu’un composant parent fasse une référence d’élément accessible à d’autres composantes, le composant parent peut :</span><span class="sxs-lookup"><span data-stu-id="72e33-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="72e33-195">Permettre aux composants pour enfants d’enregistrer les rappels.</span><span class="sxs-lookup"><span data-stu-id="72e33-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="72e33-196">Invoquez les rappels enregistrés pendant l’événement `OnAfterRender` avec la référence de l’élément passé.</span><span class="sxs-lookup"><span data-stu-id="72e33-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="72e33-197">Indirectement, cette approche permet aux composants pour enfants d’interagir avec la référence de l’élément du parent.</span><span class="sxs-lookup"><span data-stu-id="72e33-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="72e33-198">L’exemple WebAssembly suivant Blazor illustre l’approche.</span><span class="sxs-lookup"><span data-stu-id="72e33-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="72e33-199">Dans `<head>` le *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="72e33-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="72e33-200">Dans `<body>` le *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="72e33-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="72e33-201">*Pages/Index.razor* (composant parent) :</span><span class="sxs-lookup"><span data-stu-id="72e33-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="72e33-202">*Pages/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="72e33-202">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="72e33-203">*Partagé/SurveyPrompt.razor* (composant enfant):</span><span class="sxs-lookup"><span data-stu-id="72e33-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="72e33-204">*Partagé/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="72e33-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="72e33-205">Harden JS interop appels</span><span class="sxs-lookup"><span data-stu-id="72e33-205">Harden JS interop calls</span></span>

<span data-ttu-id="72e33-206">JS interop peut échouer en raison d’erreurs de réseautage et doit être traitée comme peu fiable.</span><span class="sxs-lookup"><span data-stu-id="72e33-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="72e33-207">Par défaut, Blazor une application Server passe en charge les appels JS interop sur le serveur après une minute.</span><span class="sxs-lookup"><span data-stu-id="72e33-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="72e33-208">Si une application peut tolérer un délai d’attente plus agressif, comme 10 secondes, définissez le délai d’attente à l’aide d’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="72e33-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="72e33-209">Globalement `Startup.ConfigureServices`en , spécifier le délai d’attente:</span><span class="sxs-lookup"><span data-stu-id="72e33-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="72e33-210">Per-invocation dans le code des composants, un seul appel peut spécifier le délai d’attente :</span><span class="sxs-lookup"><span data-stu-id="72e33-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="72e33-211">Pour plus d’informations sur <xref:security/blazor/server>l’épuisement des ressources, voir .</span><span class="sxs-lookup"><span data-stu-id="72e33-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="72e33-212">Évitez les références circulaires d’objets</span><span class="sxs-lookup"><span data-stu-id="72e33-212">Avoid circular object references</span></span>

<span data-ttu-id="72e33-213">Les objets qui contiennent des références circulaires ne peuvent pas être sérialisés sur le client pour l’un ou l’autre:</span><span class="sxs-lookup"><span data-stu-id="72e33-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="72e33-214">appels de méthode .NET.</span><span class="sxs-lookup"><span data-stu-id="72e33-214">.NET method calls.</span></span>
* <span data-ttu-id="72e33-215">La méthode JavaScript appelle à partir de C lorsque le type de retour a des références circulaires.</span><span class="sxs-lookup"><span data-stu-id="72e33-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="72e33-216">Pour plus d’informations, voir les questions suivantes :</span><span class="sxs-lookup"><span data-stu-id="72e33-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="72e33-217">Les références circulaires ne sont pas prises en charge, en prennent deux (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="72e33-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="72e33-218">Proposition : Ajouter un mécanisme pour gérer les références circulaires lors de la sérialisation (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="72e33-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="72e33-219">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="72e33-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="72e33-220">InteropComponent.razor exemple (dotnet/AspNetCore GitHub référentiel, branche de sortie 3.1)</span><span class="sxs-lookup"><span data-stu-id="72e33-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="72e33-221">[Effectuer de grands Blazor transferts de données dans les applications Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="72e33-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
