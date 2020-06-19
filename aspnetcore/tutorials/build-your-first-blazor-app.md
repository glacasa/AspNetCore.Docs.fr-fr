---
title: Créer votre première Blazor application
author: guardrex
description: Générez une Blazor application pas à pas.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 0b9854b3848a204b28d0427bef08364be0139069
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102825"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="2c8c5-103">Créer votre première Blazor application</span><span class="sxs-lookup"><span data-stu-id="2c8c5-103">Build your first Blazor app</span></span>

<span data-ttu-id="2c8c5-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2c8c5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2c8c5-105">Ce didacticiel vous montre comment créer et modifier une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="2c8c5-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c8c5-107">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="2c8c5-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="2c8c5-108">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="2c8c5-108">Modify Razor components</span></span>
> * <span data-ttu-id="2c8c5-109">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="2c8c5-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="2c8c5-110">Utiliser l’injection de dépendances et le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="2c8c5-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="2c8c5-111">À la fin de ce didacticiel, vous disposerez d’une application de liste de tâches en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="2c8c5-112">Construire des composants</span><span class="sxs-lookup"><span data-stu-id="2c8c5-112">Build components</span></span>

1. <span data-ttu-id="2c8c5-113">Suivez les instructions de l' <xref:blazor/get-started> article pour créer un Blazor projet pour ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="2c8c5-114">Nommez le projet *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-114">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="2c8c5-115">Accédez à chacune des trois pages de l’application dans le dossier *pages* : Hébergement, compteur et extraction de données.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-115">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="2c8c5-116">Ces pages sont implémentées par les Razor fichiers de composant *index. Razor*, *Counter. Razor*et *fetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-116">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="2c8c5-117">Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-117">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2c8c5-118">L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture de code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="2c8c5-119">Avec Blazor , vous pouvez écrire en C# à la place.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="2c8c5-120">Examinez l’implémentation du composant `Counter` dans le fichier *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-120">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="2c8c5-121">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-121">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="2c8c5-122">L’interface utilisateur du composant `Counter` est définie à l’aide de HTML.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="2c8c5-123">La logique de rendu dynamique (par exemple, les boucles, les instructions conditionnelles, les expressions) est ajoutée à l’aide d’une syntaxe C# incorporée appelée [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="2c8c5-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="2c8c5-124">Le balisage HTML et la logique de rendu C# sont convertis en une classe de composants au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="2c8c5-125">Le nom de la classe .NET générée correspond à celui du fichier.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="2c8c5-126">Les membres de la classe de composants sont définis dans un bloc `@code`.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="2c8c5-127">Dans le bloc `@code`, l’état du composant (propriétés, champs) et les méthodes sont spécifiés pour la gestion des événements ou pour définir une autre logique de composant.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="2c8c5-128">Ces membres sont ensuite utilisés dans le cadre de la logique de rendu du composant et la gestion des événements.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="2c8c5-129">Lorsque le bouton **Click me** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="2c8c5-130">Le gestionnaire `onclick` enregistré du composant `Counter` est appelé (méthode `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="2c8c5-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="2c8c5-131">Le composant `Counter` regénère son arborescence de rendu.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="2c8c5-132">La nouvelle arborescence de rendu est comparée à la précédente.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="2c8c5-133">Seules les modifications apportées à Document Object Model (DOM) sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="2c8c5-134">Le compte affiché est mis à jour.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="2c8c5-135">Modifiez la logique C# du composant `Counter` pour que l’incrément du compte passe de un à deux.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="2c8c5-136">Régénérez et exécutez l’application pour voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="2c8c5-137">Sélectionnez le bouton **Click me**.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-137">Select the **Click me** button.</span></span> <span data-ttu-id="2c8c5-138">Le compteur est incrémenté de deux.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="2c8c5-139">Utiliser des composants</span><span class="sxs-lookup"><span data-stu-id="2c8c5-139">Use components</span></span>

<span data-ttu-id="2c8c5-140">Incluez un composant dans un autre composant utilisant une syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="2c8c5-141">Ajoutez le composant `Counter` au composant `Index` de l’application en ajoutant un élément `<Counter />` au composant `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="2c8c5-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="2c8c5-142">Si vous utilisez Blazor Webassembly pour cette expérience, un `SurveyPrompt` composant est utilisé par le `Index` composant.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="2c8c5-143">Remplacez l’élément `<SurveyPrompt>` par un élément `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="2c8c5-144">Si vous utilisez une Blazor application serveur pour cette expérience, ajoutez l' `<Counter />` élément au `Index` composant :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="2c8c5-145">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-145">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="2c8c5-146">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-146">Rebuild and run the app.</span></span> <span data-ttu-id="2c8c5-147">Le composant `Index` a son propre compteur.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="2c8c5-148">Paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="2c8c5-148">Component parameters</span></span>

<span data-ttu-id="2c8c5-149">Les composants peuvent également avoir des paramètres.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-149">Components can also have parameters.</span></span> <span data-ttu-id="2c8c5-150">Les paramètres de composant sont définis à l’aide de propriétés publiques sur la classe de composant avec l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="2c8c5-151">Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="2c8c5-152">Mettez à jour le `@code` code C# du composant comme suit :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="2c8c5-153">Ajoutez une `IncrementAmount` propriété publique avec l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="2c8c5-154">Modifiez la `IncrementCount` méthode pour utiliser la `IncrementAmount` propriété lors de l’incrémentation de la valeur de `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="2c8c5-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="2c8c5-155">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-155">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="2c8c5-156">Spécifiez un paramètre `IncrementAmount` dans l’élément `<Counter>` du composant `Index` à l’aide d’un attribut.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="2c8c5-157">Définissez la valeur pour incrémenter le compteur par 10.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="2c8c5-158">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-158">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="2c8c5-159">Rechargez le composant `Index`.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-159">Reload the `Index` component.</span></span> <span data-ttu-id="2c8c5-160">Le compteur s’incrémente de dix unités chaque fois que le bouton **Cliquer ici** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-160">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="2c8c5-161">Le compteur dans le composant `Counter` continue à être incrémenté d’un.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="2c8c5-162">Acheminer vers les composants</span><span class="sxs-lookup"><span data-stu-id="2c8c5-162">Route to components</span></span>

<span data-ttu-id="2c8c5-163">La directive `@page` en haut du fichier *Counter.razor* spécifie que le composant `Counter` est un point de terminaison de routage.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-163">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="2c8c5-164">Le composant `Counter` gère les requêtes envoyées à `/counter`.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="2c8c5-165">Sans la directive `@page`, un composant ne gère pas les requêtes acheminées, mais le composant peut toujours être utilisé par d’autres composants.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="2c8c5-166">Injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="2c8c5-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="2c8c5-167">Expérience du serveur</span><span class="sxs-lookup"><span data-stu-id="2c8c5-167"> Server experience</span></span>

<span data-ttu-id="2c8c5-168">Si vous utilisez une Blazor application serveur, le `WeatherForecastService` service est inscrit en tant que [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2c8c5-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2c8c5-169">Une instance du service est disponible dans l’ensemble de l’application via l' [injection de dépendances (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="2c8c5-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="2c8c5-170">La [`@inject`](xref:mvc/views/razor#inject) directive est utilisée pour injecter l’instance du `WeatherForecastService` service dans le `FetchData` composant.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="2c8c5-171">*Pages/FetchData.razor* :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-171">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="2c8c5-172">Le composant `FetchData` utilise le service injecté, comme `ForecastService`, pour récupérer un tableau d’objets `WeatherForecast` :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="2c8c5-173">Expérience webassembly</span><span class="sxs-lookup"><span data-stu-id="2c8c5-173"> WebAssembly experience</span></span>

<span data-ttu-id="2c8c5-174">Si vous utilisez une Blazor application Webassembly, <xref:System.Net.Http.HttpClient> est injecté pour obtenir des données de prévision météorologiques à partir de l' *weather.jssur* le fichier dans le dossier *wwwroot/Sample-Data* .</span><span class="sxs-lookup"><span data-stu-id="2c8c5-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="2c8c5-175">*Pages/FetchData.razor* :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-175">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="2c8c5-176">Une [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) boucle est utilisée pour afficher chaque instance de prévision sous la forme d’une ligne dans la table des données météorologiques :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="2c8c5-177">Générer une liste de tâches</span><span class="sxs-lookup"><span data-stu-id="2c8c5-177">Build a todo list</span></span>

<span data-ttu-id="2c8c5-178">Ajoutez un nouveau composant à l’application qui implémente une liste de tâches simple.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="2c8c5-179">Ajoutez un nouveau `Todo` Razor composant à l’application dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="2c8c5-179">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="2c8c5-180">Si vous utilisez Visual Studio, cliquez avec le bouton droit sur le dossier **pages** , puis sélectionnez **Ajouter**  >  **un nouvel élément**  >  \*\* Razor composant\*\*.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-180">If you're using Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="2c8c5-181">Nommez le fichier *TODO. Razor*du composant.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-181">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="2c8c5-182">Dans d’autres environnements de développement, ajoutez un fichier vide au dossier **pages** nommé *TODO. Razor*.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-182">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="2c8c5-183">Fournissez le balisage initial pour le composant :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-183">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="2c8c5-184">Ajoutez le composant `Todo` à la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-184">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="2c8c5-185">Le composant `NavMenu` (*Shared/NavMenu.razor*) est utilisé dans la disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-185">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="2c8c5-186">Les dispositions sont des composants qui vous permettent d’éviter la duplication de contenu dans l’application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-186">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="2c8c5-187">Ajoutez un élément `<NavLink>` pour le composant `Todo` en ajoutant le balisage d’élément de liste suivant sous les éléments de liste existants dans le fichier *Shared/NavMenu.razor* :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-187">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="2c8c5-188">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-188">Rebuild and run the app.</span></span> <span data-ttu-id="2c8c5-189">Consultez la nouvelle page Todo pour vérifier que le lien vers le composant `Todo` fonctionne.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-189">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="2c8c5-190">Ajoutez un fichier *TodoItem.cs* à la racine du projet pour contenir une classe qui représente un élément Todo.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-190">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="2c8c5-191">Utilisez le code C# suivant pour la classe `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-191">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="2c8c5-192">Revenez au composant `Todo` (*Pages/Todo.razorl*) :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-192">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="2c8c5-193">Ajoutez un champ pour les éléments Todo dans un bloc `@code`.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-193">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="2c8c5-194">Le composant `Todo` utilise ce champ pour maintenir l’état de la liste de tâches.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-194">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="2c8c5-195">Ajoutez un balisage de liste non triée et une boucle `foreach` pour effectuer le rendu de chaque élément todo en tant qu’élément de liste (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="2c8c5-195">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="2c8c5-196">L’application nécessite des éléments d’interface utilisateur pour ajouter des éléments todo à la liste.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-196">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="2c8c5-197">Ajoutez une entrée de texte (`<input>`) et un bouton (`<button>`) sous la liste non ordonnée (`<ul>...</ul>`) :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-197">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="2c8c5-198">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-198">Rebuild and run the app.</span></span> <span data-ttu-id="2c8c5-199">Lorsque le bouton **Add todo** est sélectionné, rien ne se produit car aucun gestionnaire d’événements n’est lié au bouton.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-199">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="2c8c5-200">Ajoutez une méthode `AddTodo` au composant `Todo` et enregistrez-la pour les sélections du bouton à l’aide de l’attribut `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-200">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="2c8c5-201">La méthode C# `AddTodo` est appelée lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-201">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="2c8c5-202">Pour obtenir le titre du nouvel élément todo, ajoutez un champ de chaîne `newTodo` en haut du bloc `@code` et liez-le à la valeur du texte d’entrée à l’aide de l’attribut `bind` dans l’élément `<input>` :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-202">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="2c8c5-203">Mettez à jour la méthode `AddTodo` pour ajouter `TodoItem` avec le titre spécifié à la liste.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-203">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="2c8c5-204">Supprimez la valeur du texte d’entrée en définissant `newTodo` sur une chaîne vide :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-204">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="2c8c5-205">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-205">Rebuild and run the app.</span></span> <span data-ttu-id="2c8c5-206">Ajoutez quelques éléments todo à la liste Todo pour tester le nouveau code.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-206">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="2c8c5-207">Le texte du titre pour chaque élément todo peut être rendu modifiable et une case à cocher peut aider l’utilisateur à effectuer le suivi des éléments terminés.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-207">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="2c8c5-208">Ajoutez une entrée de case à cocher pour chaque élément todo et liez sa valeur à la propriété `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-208">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="2c8c5-209">Remplacez `@todo.Title` par un élément `<input>` lié à `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-209">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="2c8c5-210">Pour vérifier que ces valeurs sont liées, mettez à jour l’en-tête `<h3>` pour afficher le nombre d’éléments todo qui ne sont pas terminés (`IsDone` est `false`).</span><span class="sxs-lookup"><span data-stu-id="2c8c5-210">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="2c8c5-211">Le composant `Todo` (*Pages/Todo.razor*) terminé :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-211">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="2c8c5-212">Régénérez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-212">Rebuild and run the app.</span></span> <span data-ttu-id="2c8c5-213">Ajoutez des éléments todo pour tester le nouveau code.</span><span class="sxs-lookup"><span data-stu-id="2c8c5-213">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2c8c5-214">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="2c8c5-214">Next steps</span></span>

<span data-ttu-id="2c8c5-215">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-215">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c8c5-216">Créer un projet d’application de liste de tâches Blazor</span><span class="sxs-lookup"><span data-stu-id="2c8c5-216">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="2c8c5-217">Modifier les Razor composants</span><span class="sxs-lookup"><span data-stu-id="2c8c5-217">Modify Razor components</span></span>
> * <span data-ttu-id="2c8c5-218">Utiliser la gestion des événements et la liaison de données dans les composants</span><span class="sxs-lookup"><span data-stu-id="2c8c5-218">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="2c8c5-219">Utiliser l’injection de dépendances et le routage dans une Blazor application</span><span class="sxs-lookup"><span data-stu-id="2c8c5-219">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="2c8c5-220">Découvrez comment créer et utiliser des composants :</span><span class="sxs-lookup"><span data-stu-id="2c8c5-220">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
