---
title: Chargement différé d’assemblys dans ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment charger en différé des assemblys dans des Blazor WebAssembly applications ASP.net core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 46f98080ad40f614f9cb1af2190f263d205c1016
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865157"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="519f2-103">Chargement différé d’assemblys dans ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="519f2-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="519f2-104">Par [safia Abdalla](https://safia.rocks) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="519f2-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="519f2-105">Blazor WebAssembly les performances de démarrage de l’application peuvent être améliorées en différant le chargement de certains assemblys d’application jusqu’à ce qu’ils soient nécessaires, ce qui s’appelle le *chargement différé*.</span><span class="sxs-lookup"><span data-stu-id="519f2-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="519f2-106">Par exemple, les assemblys qui sont utilisés uniquement pour le rendu d’un seul composant peuvent être configurés pour être chargés uniquement si l’utilisateur accède à ce composant.</span><span class="sxs-lookup"><span data-stu-id="519f2-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="519f2-107">Après le chargement, les assemblys sont mis en cache côté client et sont disponibles pour toutes les navigations ultérieures.</span><span class="sxs-lookup"><span data-stu-id="519f2-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="519f2-108">Blazorla fonctionnalité de chargement différé de vous permet de marquer des assemblys d’application pour le chargement différé, qui charge les assemblys lors de l’exécution lorsque l’utilisateur accède à un itinéraire particulier.</span><span class="sxs-lookup"><span data-stu-id="519f2-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="519f2-109">La fonctionnalité est constituée de modifications apportées au fichier projet et des modifications apportées au routeur de l’application.</span><span class="sxs-lookup"><span data-stu-id="519f2-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="519f2-110">Le chargement différé d’un assembly n’avantage pas Blazor Server les applications, car les assemblys ne sont pas téléchargés vers le client dans une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="519f2-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="519f2-111">Fichier projet</span><span class="sxs-lookup"><span data-stu-id="519f2-111">Project file</span></span>

<span data-ttu-id="519f2-112">Marquez les assemblys pour le chargement différé dans le fichier projet de l’application ( `.csproj` ) à l’aide de l' `BlazorWebAssemblyLazyLoad` élément.</span><span class="sxs-lookup"><span data-stu-id="519f2-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="519f2-113">Utilisez le nom de l’assembly sans l' `.dll` extension.</span><span class="sxs-lookup"><span data-stu-id="519f2-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="519f2-114">L' Blazor infrastructure empêche le chargement des assemblys spécifiés par ce groupe d’éléments au lancement de l’application.</span><span class="sxs-lookup"><span data-stu-id="519f2-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="519f2-115">L’exemple suivant marque un grand assembly personnalisé ( `GrantImaharaRobotControls.dll` ) pour le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="519f2-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="519f2-116">Si un assembly marqué pour le chargement différé a des dépendances, il doit également être marqué pour le chargement différé dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="519f2-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="519f2-117">Seuls les assemblys utilisés par l’application peuvent être chargés de manière différée.</span><span class="sxs-lookup"><span data-stu-id="519f2-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="519f2-118">L’éditeur de liens supprime les assemblys inutilisés de la sortie publiée.</span><span class="sxs-lookup"><span data-stu-id="519f2-118">The linker strips unused assemblies from published output.</span></span>

> [!NOTE]
> <span data-ttu-id="519f2-119">Dans .NET 5 version Release Candidate 1 (RC1) ou version ultérieure, qui sera publiée le mi-septembre, le nom de l’assembly nécessitera l' `.dll` extension suivante :</span><span class="sxs-lookup"><span data-stu-id="519f2-119">In .NET 5 Release Candidate 1 (RC1) or later, which will be released in mid-September, the assembly name will require the `.dll` extension:</span></span>
>
> ```xml
> <ItemGroup>
>  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
> </ItemGroup>
> ```

## <a name="router-component"></a><span data-ttu-id="519f2-120">Composant `Router`</span><span class="sxs-lookup"><span data-stu-id="519f2-120">`Router` component</span></span>

<span data-ttu-id="519f2-121">Blazor`Router`le composant de désigne les assemblys qui Blazor recherchent les composants routables.</span><span class="sxs-lookup"><span data-stu-id="519f2-121">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="519f2-122">Le `Router` composant est également responsable du rendu du composant pour l’itinéraire à partir duquel l’utilisateur navigue.</span><span class="sxs-lookup"><span data-stu-id="519f2-122">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="519f2-123">Le `Router` composant prend en charge une `OnNavigateAsync` fonctionnalité qui peut être utilisée conjointement avec le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="519f2-123">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="519f2-124">Dans le composant de l’application `Router` ( `App.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="519f2-124">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="519f2-125">Ajoutez un `OnNavigateAsync` rappel.</span><span class="sxs-lookup"><span data-stu-id="519f2-125">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="519f2-126">Le `OnNavigateAsync` gestionnaire est appelé lorsque l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="519f2-126">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="519f2-127">Visite un itinéraire pour la première fois en y accédant directement à partir de son navigateur.</span><span class="sxs-lookup"><span data-stu-id="519f2-127">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="519f2-128">Navigue vers un nouvel itinéraire à l’aide d’un lien ou d’un <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> appel.</span><span class="sxs-lookup"><span data-stu-id="519f2-128">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="519f2-129">Si les assemblys à chargement différé contiennent des composants routables, ajoutez une [liste](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (par exemple, nommée `lazyLoadedAssemblies` ) au composant.</span><span class="sxs-lookup"><span data-stu-id="519f2-129">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="519f2-130">Les assemblys sont passés à la <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection au cas où les assemblys contiennent des composants routables.</span><span class="sxs-lookup"><span data-stu-id="519f2-130">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="519f2-131">L’infrastructure recherche les itinéraires dans les assemblys et met à jour la collection d’itinéraires si de nouveaux itinéraires sont trouvés.</span><span class="sxs-lookup"><span data-stu-id="519f2-131">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

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

<span data-ttu-id="519f2-132">Si le `OnNavigateAsync` rappel lève une exception non gérée, l' [ Blazor interface utilisateur d’erreur](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) est appelée.</span><span class="sxs-lookup"><span data-stu-id="519f2-132">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="519f2-133">Logique de chargement d’assembly dans `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="519f2-133">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="519f2-134">`OnNavigateAsync` a un `NavigationContext` paramètre qui fournit des informations sur l’événement de navigation asynchrone actuel, y compris le chemin d’accès cible ( `Path` ) et le jeton d’annulation ( `CancellationToken` ) :</span><span class="sxs-lookup"><span data-stu-id="519f2-134">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="519f2-135">La `Path` propriété est le chemin d’accès de destination de l’utilisateur par rapport au chemin d’accès de base de l’application, par exemple `/robot` .</span><span class="sxs-lookup"><span data-stu-id="519f2-135">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="519f2-136">Le `CancellationToken` peut être utilisé pour observer l’annulation de la tâche asynchrone.</span><span class="sxs-lookup"><span data-stu-id="519f2-136">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="519f2-137">`OnNavigateAsync` annule automatiquement la tâche de navigation en cours d’exécution lorsque l’utilisateur accède à une autre page.</span><span class="sxs-lookup"><span data-stu-id="519f2-137">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="519f2-138">Dans `OnNavigateAsync` , implémentez une logique pour déterminer les assemblys à charger.</span><span class="sxs-lookup"><span data-stu-id="519f2-138">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="519f2-139">Les options sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="519f2-139">Options include:</span></span>

* <span data-ttu-id="519f2-140">Vérifications conditionnelles à l’intérieur de la `OnNavigateAsync` méthode.</span><span class="sxs-lookup"><span data-stu-id="519f2-140">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="519f2-141">Table de recherche qui mappe des itinéraires à des noms d’assemblys, soit injectés dans le composant, soit implémentée dans le [`@code`](xref:mvc/views/razor#code) bloc.</span><span class="sxs-lookup"><span data-stu-id="519f2-141">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="519f2-142">`LazyAssemblyLoader` est un service Singleton fourni par l’infrastructure pour le chargement d’assemblys.</span><span class="sxs-lookup"><span data-stu-id="519f2-142">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="519f2-143">Injecter `LazyAssemblyLoader` dans le `Router` composant :</span><span class="sxs-lookup"><span data-stu-id="519f2-143">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="519f2-144">Le `LazyAssemblyLoader` fournit la `LoadAssembliesAsync` méthode qui :</span><span class="sxs-lookup"><span data-stu-id="519f2-144">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="519f2-145">Utilise l’interopérabilité JS pour extraire des assemblys via un appel réseau.</span><span class="sxs-lookup"><span data-stu-id="519f2-145">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="519f2-146">Charge les assemblys dans le runtime s’exécutant sur webassembly dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="519f2-146">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="519f2-147">L’implémentation du chargement différé de l’infrastructure prend en charge le prérendu sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="519f2-147">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="519f2-148">Lors du prérendu, tous les assemblys, y compris ceux marqués pour le chargement différé, sont supposés être chargés.</span><span class="sxs-lookup"><span data-stu-id="519f2-148">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="519f2-149">Interaction de l’utilisateur avec le `<Navigating>` contenu</span><span class="sxs-lookup"><span data-stu-id="519f2-149">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="519f2-150">Lors du chargement des assemblys, ce qui peut prendre plusieurs secondes, le `Router` composant peut indiquer à l’utilisateur qu’une transition de page se produit :</span><span class="sxs-lookup"><span data-stu-id="519f2-150">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="519f2-151">Ajoutez une [`@using`](xref:mvc/views/razor#using) directive pour l' <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> espace de noms.</span><span class="sxs-lookup"><span data-stu-id="519f2-151">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="519f2-152">Ajoutez une `<Navigating>` balise au composant avec le balisage à afficher pendant les événements de transition de page.</span><span class="sxs-lookup"><span data-stu-id="519f2-152">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

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

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="519f2-153">Gérer les annulations dans `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="519f2-153">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="519f2-154">L' `NavigationContext` objet passé au `OnNavigateAsync` rappel contient un `CancellationToken` qui est défini lorsqu’un nouvel événement de navigation se produit.</span><span class="sxs-lookup"><span data-stu-id="519f2-154">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="519f2-155">Le `OnNavigateAsync` rappel doit lever une exception quand ce jeton d’annulation est défini pour éviter de continuer à exécuter le `OnNavigateAsync` rappel sur une navigation obsolète.</span><span class="sxs-lookup"><span data-stu-id="519f2-155">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="519f2-156">Si un utilisateur accède à l’itinéraire A, puis immédiatement à l’itinéraire B, l’application ne doit pas continuer à exécuter le `OnNavigateAsync` rappel pour l’itinéraire a :</span><span class="sxs-lookup"><span data-stu-id="519f2-156">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

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
> <span data-ttu-id="519f2-157">Ne pas lever la valeur si le jeton d’annulation dans `NavigationContext` est annulé peut entraîner un comportement inattendu, tel que le rendu d’un composant à partir d’une navigation précédente.</span><span class="sxs-lookup"><span data-stu-id="519f2-157">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="519f2-158">`OnNavigateAsync` événements et fichiers d’assembly renommés</span><span class="sxs-lookup"><span data-stu-id="519f2-158">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="519f2-159">Le chargeur de ressource s’appuie sur les noms d’assemblys qui sont définis dans le `blazor.boot.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="519f2-159">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="519f2-160">Si les [assemblys sont renommés](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), les noms d’assembly utilisés dans `OnNavigateAsync` les méthodes et les noms d’assembly dans le `blazor.boot.json` fichier ne sont pas synchronisés.</span><span class="sxs-lookup"><span data-stu-id="519f2-160">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="519f2-161">Pour rectifier ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="519f2-161">To rectify this:</span></span>

* <span data-ttu-id="519f2-162">Vérifiez si l’application s’exécute dans l’environnement de production lorsque vous déterminez les noms d’assembly à utiliser.</span><span class="sxs-lookup"><span data-stu-id="519f2-162">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="519f2-163">Stockez les noms d’assemblys renommés dans un fichier distinct et lisez-les à partir de ce fichier pour déterminer le nom de l’assembly à utiliser dans les `LazyLoadAssemblyService` `OnNavigateAsync` méthodes et.</span><span class="sxs-lookup"><span data-stu-id="519f2-163">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="519f2-164">Exemple complet</span><span class="sxs-lookup"><span data-stu-id="519f2-164">Complete example</span></span>

<span data-ttu-id="519f2-165">Le composant complet suivant `Router` montre le chargement de l' `GrantImaharaRobotControls.dll` assembly lorsque l’utilisateur accède à `/robot` .</span><span class="sxs-lookup"><span data-stu-id="519f2-165">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="519f2-166">Lors des transitions de page, un message stylisé est affiché à l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="519f2-166">During page transitions, a styled message is displayed to the user.</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="519f2-167">Dépanner</span><span class="sxs-lookup"><span data-stu-id="519f2-167">Troubleshoot</span></span>

* <span data-ttu-id="519f2-168">Si un rendu inattendu se produit (par exemple, un composant d’une navigation précédente est rendu), confirmez que le code lève si le jeton d’annulation est défini.</span><span class="sxs-lookup"><span data-stu-id="519f2-168">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="519f2-169">Si les assemblys sont toujours chargés au démarrage de l’application, vérifiez que l’assembly est marqué comme étant différé dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="519f2-169">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="519f2-170">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="519f2-170">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
