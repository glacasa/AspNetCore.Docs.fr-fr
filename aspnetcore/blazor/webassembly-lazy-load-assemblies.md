---
title: Chargement différé d’assemblys dans ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Découvrez comment charger en différé des assemblys dans des Blazor WebAssembly applications ASP.net core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0ce03badccad4e06aa3c316580ab82be38a806c6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013370"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="1d603-103">Chargement différé d’assemblys dans ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1d603-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="1d603-104">Par [safia Abdalla](https://safia.rocks) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d603-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1d603-105">Blazor WebAssemblyles performances de démarrage de l’application peuvent être améliorées en différant le chargement de certains assemblys d’application jusqu’à ce qu’ils soient nécessaires, ce qui s’appelle le *chargement différé*.</span><span class="sxs-lookup"><span data-stu-id="1d603-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="1d603-106">Par exemple, les assemblys qui sont utilisés uniquement pour le rendu d’un seul composant peuvent être configurés pour être chargés uniquement si l’utilisateur accède à ce composant.</span><span class="sxs-lookup"><span data-stu-id="1d603-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="1d603-107">Après le chargement, les assemblys sont mis en cache côté client et sont disponibles pour toutes les navigations ultérieures.</span><span class="sxs-lookup"><span data-stu-id="1d603-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="1d603-108">Blazorla fonctionnalité de chargement différé de vous permet de marquer des assemblys d’application pour le chargement différé, qui charge les assemblys lors de l’exécution lorsque l’utilisateur accède à un itinéraire particulier.</span><span class="sxs-lookup"><span data-stu-id="1d603-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="1d603-109">La fonctionnalité est constituée de modifications apportées au fichier projet et des modifications apportées au routeur de l’application.</span><span class="sxs-lookup"><span data-stu-id="1d603-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="1d603-110">Le chargement différé d’un assembly n’avantage pas Blazor Server les applications, car les assemblys ne sont pas téléchargés vers le client dans une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="1d603-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="1d603-111">Fichier projet</span><span class="sxs-lookup"><span data-stu-id="1d603-111">Project file</span></span>

<span data-ttu-id="1d603-112">Marquez les assemblys pour le chargement différé dans le fichier projet de l’application ( `.csproj` ) à l’aide de l' `BlazorWebAssemblyLazyLoad` élément.</span><span class="sxs-lookup"><span data-stu-id="1d603-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="1d603-113">Utilisez le nom de l’assembly sans l' `.dll` extension.</span><span class="sxs-lookup"><span data-stu-id="1d603-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="1d603-114">L' Blazor infrastructure empêche le chargement des assemblys spécifiés par ce groupe d’éléments au lancement de l’application.</span><span class="sxs-lookup"><span data-stu-id="1d603-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="1d603-115">L’exemple suivant marque un grand assembly personnalisé ( `GrantImaharaRobotControls.dll` ) pour le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="1d603-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="1d603-116">Si un assembly marqué pour le chargement différé a des dépendances, il doit également être marqué pour le chargement différé dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="1d603-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="1d603-117">Seuls les assemblys utilisés par l’application peuvent être chargés de manière différée.</span><span class="sxs-lookup"><span data-stu-id="1d603-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="1d603-118">L’éditeur de liens supprime les assemblys inutilisés de la sortie publiée.</span><span class="sxs-lookup"><span data-stu-id="1d603-118">The linker strips unused assemblies from published output.</span></span>

## <a name="router-component"></a><span data-ttu-id="1d603-119">Composant `Router`</span><span class="sxs-lookup"><span data-stu-id="1d603-119">`Router` component</span></span>

<span data-ttu-id="1d603-120">Blazor`Router`le composant de désigne les assemblys qui Blazor recherchent les composants routables.</span><span class="sxs-lookup"><span data-stu-id="1d603-120">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="1d603-121">Le `Router` composant est également responsable du rendu du composant pour l’itinéraire à partir duquel l’utilisateur navigue.</span><span class="sxs-lookup"><span data-stu-id="1d603-121">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="1d603-122">Le `Router` composant prend en charge une `OnNavigateAsync` fonctionnalité qui peut être utilisée conjointement avec le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="1d603-122">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="1d603-123">Dans le composant de l’application `Router` ( `App.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="1d603-123">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="1d603-124">Ajoutez un `OnNavigateAsync` rappel.</span><span class="sxs-lookup"><span data-stu-id="1d603-124">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="1d603-125">Le `OnNavigateAsync` gestionnaire est appelé lorsque l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="1d603-125">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="1d603-126">Visite un itinéraire pour la première fois en y accédant directement à partir de son navigateur.</span><span class="sxs-lookup"><span data-stu-id="1d603-126">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="1d603-127">Navigue vers un nouvel itinéraire à l’aide d’un lien ou d’un <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> appel.</span><span class="sxs-lookup"><span data-stu-id="1d603-127">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="1d603-128">Si les assemblys à chargement différé contiennent des composants routables, ajoutez une [liste](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (par exemple, nommée `lazyLoadedAssemblies` ) au composant.</span><span class="sxs-lookup"><span data-stu-id="1d603-128">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="1d603-129">Les assemblys sont passés à la <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection au cas où les assemblys contiennent des composants routables.</span><span class="sxs-lookup"><span data-stu-id="1d603-129">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="1d603-130">L’infrastructure recherche les itinéraires dans les assemblys et met à jour la collection d’itinéraires si de nouveaux itinéraires sont trouvés.</span><span class="sxs-lookup"><span data-stu-id="1d603-130">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="1d603-131">Si le `OnNavigateAsync` rappel lève une exception non gérée, l' [ Blazor interface utilisateur d’erreur](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) est appelée.</span><span class="sxs-lookup"><span data-stu-id="1d603-131">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="1d603-132">Logique de chargement d’assembly dans`OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="1d603-132">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="1d603-133">`OnNavigateAsync`a un `NavigationContext` paramètre qui fournit des informations sur l’événement de navigation asynchrone actuel, y compris le chemin d’accès cible ( `Path` ) et le jeton d’annulation ( `CancellationToken` ) :</span><span class="sxs-lookup"><span data-stu-id="1d603-133">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="1d603-134">La `Path` propriété est le chemin d’accès de destination de l’utilisateur par rapport au chemin d’accès de base de l’application, par exemple `/robot` .</span><span class="sxs-lookup"><span data-stu-id="1d603-134">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="1d603-135">Le `CancellationToken` peut être utilisé pour observer l’annulation de la tâche asynchrone.</span><span class="sxs-lookup"><span data-stu-id="1d603-135">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="1d603-136">`OnNavigateAsync`annule automatiquement la tâche de navigation en cours d’exécution lorsque l’utilisateur accède à une autre page.</span><span class="sxs-lookup"><span data-stu-id="1d603-136">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="1d603-137">Dans `OnNavigateAsync` , implémentez une logique pour déterminer les assemblys à charger.</span><span class="sxs-lookup"><span data-stu-id="1d603-137">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="1d603-138">Options disponibles :</span><span class="sxs-lookup"><span data-stu-id="1d603-138">Options include:</span></span>

* <span data-ttu-id="1d603-139">Vérifications conditionnelles à l’intérieur de la `OnNavigateAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="1d603-139">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="1d603-140">Table de recherche qui mappe des itinéraires à des noms d’assemblys, soit injectés dans le composant, soit implémentée dans le [`@code`](xref:mvc/views/razor#code) bloc.</span><span class="sxs-lookup"><span data-stu-id="1d603-140">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="1d603-141">`LazyAssemblyLoader`est un service Singleton fourni par l’infrastructure pour le chargement d’assemblys.</span><span class="sxs-lookup"><span data-stu-id="1d603-141">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="1d603-142">Injecter `LazyAssemblyLoader` dans le `Router` composant :</span><span class="sxs-lookup"><span data-stu-id="1d603-142">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="1d603-143">Le `LazyAssemblyLoader` fournit la `LoadAssembliesAsync` méthode qui :</span><span class="sxs-lookup"><span data-stu-id="1d603-143">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="1d603-144">Utilise l’interopérabilité JS pour extraire des assemblys via un appel réseau.</span><span class="sxs-lookup"><span data-stu-id="1d603-144">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="1d603-145">Charge les assemblys dans le runtime s’exécutant sur webassembly dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="1d603-145">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="1d603-146">L’implémentation du chargement différé de l’infrastructure prend en charge le prérendu sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="1d603-146">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="1d603-147">Lors du prérendu, tous les assemblys, y compris ceux marqués pour le chargement différé, sont supposés être chargés.</span><span class="sxs-lookup"><span data-stu-id="1d603-147">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="1d603-148">Interaction de l’utilisateur avec le `<Navigating>` contenu</span><span class="sxs-lookup"><span data-stu-id="1d603-148">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="1d603-149">Lors du chargement des assemblys, ce qui peut prendre plusieurs secondes, le `Router` composant peut indiquer à l’utilisateur qu’une transition de page se produit :</span><span class="sxs-lookup"><span data-stu-id="1d603-149">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="1d603-150">Ajoutez une [`@using`](xref:mvc/views/razor#using) directive pour l' <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> espace de noms.</span><span class="sxs-lookup"><span data-stu-id="1d603-150">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="1d603-151">Ajoutez une `<Navigating>` balise au composant avec le balisage à afficher pendant les événements de transition de page.</span><span class="sxs-lookup"><span data-stu-id="1d603-151">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="1d603-152">Gérer les annulations dans`OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="1d603-152">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="1d603-153">L' `NavigationContext` objet passé au `OnNavigateAsync` rappel contient un `CancellationToken` qui est défini lorsqu’un nouvel événement de navigation se produit.</span><span class="sxs-lookup"><span data-stu-id="1d603-153">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="1d603-154">Le `OnNavigateAsync` rappel doit lever une exception quand ce jeton d’annulation est défini pour éviter de continuer à exécuter le `OnNavigateAsync` rappel sur une navigation obsolète.</span><span class="sxs-lookup"><span data-stu-id="1d603-154">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="1d603-155">Si un utilisateur accède à l’itinéraire A, puis immédiatement à l’itinéraire B, l’application ne doit pas continuer à exécuter le `OnNavigateAsync` rappel pour l’itinéraire a :</span><span class="sxs-lookup"><span data-stu-id="1d603-155">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="1d603-156">Ne pas lever la valeur si le jeton d’annulation dans `NavigationContext` est annulé peut entraîner un comportement inattendu, tel que le rendu d’un composant à partir d’une navigation précédente.</span><span class="sxs-lookup"><span data-stu-id="1d603-156">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="complete-example"></a><span data-ttu-id="1d603-157">Exemple complet</span><span class="sxs-lookup"><span data-stu-id="1d603-157">Complete example</span></span>

<span data-ttu-id="1d603-158">Le composant complet suivant `Router` montre le chargement de l' `GrantImaharaRobotControls.dll` assembly lorsque l’utilisateur accède à `/robot` .</span><span class="sxs-lookup"><span data-stu-id="1d603-158">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="1d603-159">Lors des transitions de page, un message stylisé est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="1d603-159">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="1d603-160">Dépanner</span><span class="sxs-lookup"><span data-stu-id="1d603-160">Troubleshoot</span></span>

* <span data-ttu-id="1d603-161">Si un rendu inattendu se produit (par exemple, un composant d’une navigation précédente est rendu), confirmez que le code lève si le jeton d’annulation est défini.</span><span class="sxs-lookup"><span data-stu-id="1d603-161">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="1d603-162">Si les assemblys sont toujours chargés au démarrage de l’application, vérifiez que l’assembly est marqué comme étant différé dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="1d603-162">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d603-163">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1d603-163">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
