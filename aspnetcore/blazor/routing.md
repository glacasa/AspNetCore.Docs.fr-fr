---
title: Routage Blazor de ASP.net Core
author: guardrex
description: Découvrez comment acheminer des requêtes dans des applications et sur le composant NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 291ec33f951008df10f85336c7abd0b3d0a50bbc
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727721"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="bec41-103">ASP.NET Core du routage éblouissant</span><span class="sxs-lookup"><span data-stu-id="bec41-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="bec41-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bec41-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="bec41-105">Découvrez comment acheminer les demandes et comment utiliser le `NavLink` composant pour créer des liens de navigation dans les applications éblouissantes.</span><span class="sxs-lookup"><span data-stu-id="bec41-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="bec41-106">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bec41-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="bec41-107">Le serveur éblouissant est intégré à [ASP.net Core routage des points de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="bec41-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="bec41-108">Une application ASP.NET Core est configurée pour accepter les connexions entrantes pour `MapBlazorHub` les `Startup.Configure`composants interactifs avec dans :</span><span class="sxs-lookup"><span data-stu-id="bec41-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="bec41-109">La configuration la plus courante consiste à acheminer toutes les demandes vers une page Razor, qui joue le rôle d’hôte pour la partie côté serveur de l’application serveur éblouissante.</span><span class="sxs-lookup"><span data-stu-id="bec41-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="bec41-110">Par Convention, la page *hôte* est généralement nommée *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bec41-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="bec41-111">L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="bec41-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="bec41-112">L’itinéraire de secours est pris en compte lorsque les autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="bec41-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="bec41-113">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l’application serveur éblouissante.</span><span class="sxs-lookup"><span data-stu-id="bec41-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="bec41-114">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="bec41-114">Route templates</span></span>

<span data-ttu-id="bec41-115">Le `Router` composant active le routage vers chaque composant avec un itinéraire spécifié.</span><span class="sxs-lookup"><span data-stu-id="bec41-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="bec41-116">Le `Router` composant apparaît dans le fichier *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="bec41-116">The `Router` component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="bec41-117">Lorsqu’un fichier *. Razor* avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="bec41-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="bec41-118">Au moment de l' `RouteView` exécution, le composant :</span><span class="sxs-lookup"><span data-stu-id="bec41-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="bec41-119">Reçoit du `RouteData` `Router` avec les paramètres souhaités.</span><span class="sxs-lookup"><span data-stu-id="bec41-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="bec41-120">Restitue le composant spécifié avec sa disposition (ou une disposition par défaut facultative) à l’aide des paramètres spécifiés.</span><span class="sxs-lookup"><span data-stu-id="bec41-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="bec41-121">Vous pouvez éventuellement spécifier un `DefaultLayout` paramètre avec une classe de disposition à utiliser pour les composants qui ne spécifient pas de disposition.</span><span class="sxs-lookup"><span data-stu-id="bec41-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="bec41-122">Les modèles éblouissants par défaut spécifient le `MainLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="bec41-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="bec41-123">*MainLayout. Razor* se trouve dans le dossier *partagé* du projet de modèle.</span><span class="sxs-lookup"><span data-stu-id="bec41-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="bec41-124">Pour plus d’informations sur les mises en <xref:blazor/layouts>page, consultez.</span><span class="sxs-lookup"><span data-stu-id="bec41-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="bec41-125">Plusieurs modèles de routage peuvent être appliqués à un composant.</span><span class="sxs-lookup"><span data-stu-id="bec41-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="bec41-126">Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="bec41-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="bec41-127">Pour que les URL soient correctement résolues, l’application `<base>` doit inclure une balise dans son fichier *wwwroot/index.html* (éblouissant webassembly) ou le fichier *pages/_Host. cshtml* (serveur éblouissant) avec le chemin d' `href` accès de`<base href="/">`base de l’application spécifié dans l’attribut ().</span><span class="sxs-lookup"><span data-stu-id="bec41-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="bec41-128">Pour plus d’informations, consultez <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="bec41-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="bec41-129">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="bec41-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="bec41-130">Le `Router` composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="bec41-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="bec41-131">Dans le fichier *app. Razor* , définissez le contenu personnalisé dans `NotFound` le paramètre de modèle `Router` du composant :</span><span class="sxs-lookup"><span data-stu-id="bec41-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="bec41-132">Le contenu des `<NotFound>` balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="bec41-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="bec41-133">Pour appliquer une disposition par défaut `NotFound` au contenu, <xref:blazor/layouts>consultez.</span><span class="sxs-lookup"><span data-stu-id="bec41-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="bec41-134">Acheminer vers des composants à partir de plusieurs assemblys</span><span class="sxs-lookup"><span data-stu-id="bec41-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="bec41-135">Utilisez le `AdditionalAssemblies` paramètre pour spécifier des assemblys supplémentaires que `Router` le composant doit prendre en compte lors de la recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="bec41-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="bec41-136">Les assemblys spécifiés sont pris en compte `AppAssembly`en plus de l’assembly spécifié.</span><span class="sxs-lookup"><span data-stu-id="bec41-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="bec41-137">Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de classes référencée.</span><span class="sxs-lookup"><span data-stu-id="bec41-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="bec41-138">L’exemple `AdditionalAssemblies` suivant entraîne la prise en charge `Component1`du routage pour :</span><span class="sxs-lookup"><span data-stu-id="bec41-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="bec41-139">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="bec41-139">Route parameters</span></span>

<span data-ttu-id="bec41-140">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse) :</span><span class="sxs-lookup"><span data-stu-id="bec41-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="bec41-141">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="bec41-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="bec41-142">Deux `@page` directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="bec41-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="bec41-143">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="bec41-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="bec41-144">La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à `Text` la propriété.</span><span class="sxs-lookup"><span data-stu-id="bec41-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="bec41-145">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="bec41-145">Route constraints</span></span>

<span data-ttu-id="bec41-146">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="bec41-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="bec41-147">Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si :</span><span class="sxs-lookup"><span data-stu-id="bec41-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="bec41-148">Un `Id` segment de routage est présent sur l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="bec41-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="bec41-149">Le `Id` segment est un entier (`int`).</span><span class="sxs-lookup"><span data-stu-id="bec41-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="bec41-150">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="bec41-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="bec41-151">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="bec41-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="bec41-152">Contrainte</span><span class="sxs-lookup"><span data-stu-id="bec41-152">Constraint</span></span> | <span data-ttu-id="bec41-153"> Exemple</span><span class="sxs-lookup"><span data-stu-id="bec41-153">Example</span></span>           | <span data-ttu-id="bec41-154">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="bec41-154">Example Matches</span></span>                                                                  | <span data-ttu-id="bec41-155">Invariant</span><span class="sxs-lookup"><span data-stu-id="bec41-155">Invariant</span></span><br><span data-ttu-id="bec41-156">culture</span><span class="sxs-lookup"><span data-stu-id="bec41-156">culture</span></span><br><span data-ttu-id="bec41-157">correspondance</span><span class="sxs-lookup"><span data-stu-id="bec41-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="bec41-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="bec41-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="bec41-159">Non </span><span class="sxs-lookup"><span data-stu-id="bec41-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="bec41-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="bec41-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="bec41-161">Oui</span><span class="sxs-lookup"><span data-stu-id="bec41-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="bec41-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="bec41-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="bec41-163">Oui</span><span class="sxs-lookup"><span data-stu-id="bec41-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="bec41-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="bec41-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="bec41-165">Oui</span><span class="sxs-lookup"><span data-stu-id="bec41-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="bec41-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="bec41-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="bec41-167">Oui</span><span class="sxs-lookup"><span data-stu-id="bec41-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="bec41-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="bec41-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="bec41-169">Non </span><span class="sxs-lookup"><span data-stu-id="bec41-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="bec41-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="bec41-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="bec41-171">Oui</span><span class="sxs-lookup"><span data-stu-id="bec41-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="bec41-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="bec41-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="bec41-173">Oui</span><span class="sxs-lookup"><span data-stu-id="bec41-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="bec41-174">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="bec41-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="bec41-175">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="bec41-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="bec41-176">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="bec41-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="bec41-177">Dans les applications serveur éblouissantes, l’itinéraire par défaut dans *_Host. cshtml* est `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="bec41-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="bec41-178">Une URL de demande qui contient un point`.`() ne correspond pas à l’itinéraire par défaut, car l’URL semble demander un fichier.</span><span class="sxs-lookup"><span data-stu-id="bec41-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="bec41-179">Une application éblouissant retourne une réponse *404-introuvable* pour un fichier statique qui n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="bec41-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="bec41-180">Pour utiliser des itinéraires qui contiennent un point, configurez *_Host. cshtml* avec le modèle d’itinéraire suivant :</span><span class="sxs-lookup"><span data-stu-id="bec41-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="bec41-181">Le `"/{**path}"` modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="bec41-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="bec41-182">Double-astérisque syntaxe *catch-all* (`**`) pour capturer le chemin d’accès dans plusieurs limites de dossiers sans encodage`/`des barres obliques ().</span><span class="sxs-lookup"><span data-stu-id="bec41-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="bec41-183">`path`nom du paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="bec41-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="bec41-184">La syntaxe de paramètre *catch-all* (`*`/`**`) n’est **pas** prise en charge dans les composants Razor (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="bec41-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="bec41-185">Pour plus d’informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="bec41-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="bec41-186">Composant NavLink</span><span class="sxs-lookup"><span data-stu-id="bec41-186">NavLink component</span></span>

<span data-ttu-id="bec41-187">Utilisez un `NavLink` composant à la place des éléments Hyperlink`<a>`html () lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="bec41-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="bec41-188">Un `NavLink` composant se comporte comme un `<a>` élément, sauf qu’il fait basculer `active` une classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="bec41-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="bec41-189">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="bec41-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="bec41-190">Le composant `NavMenu` suivant crée une barre de navigation de [démarrage](https://getbootstrap.com/docs/) qui montre comment `NavLink` utiliser des composants :</span><span class="sxs-lookup"><span data-stu-id="bec41-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="bec41-191">Il existe deux `NavLinkMatch` options que vous pouvez assigner `Match` à l’attribut `<NavLink>` de l’élément :</span><span class="sxs-lookup"><span data-stu-id="bec41-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="bec41-192">`NavLinkMatch.All`&ndash; Est actif lorsqu’il correspond à la totalité de l’URL `NavLink` actuelle.</span><span class="sxs-lookup"><span data-stu-id="bec41-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="bec41-193">`NavLinkMatch.Prefix`(*par défaut*) &ndash; Est actif lorsqu’il correspond à n’importe quel préfixe de l’URL `NavLink` actuelle.</span><span class="sxs-lookup"><span data-stu-id="bec41-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="bec41-194">Dans l’exemple précédent, la page `NavLink` `href=""` d’hébergement correspond à l’URL de base `active` et reçoit uniquement la classe CSS à l’URL du chemin de base par `https://localhost:5001/`défaut de l’application (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="bec41-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="bec41-195">Le deuxième `NavLink` reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple `https://localhost:5001/MyComponent` , `https://localhost:5001/MyComponent/AnotherSegment`et).</span><span class="sxs-lookup"><span data-stu-id="bec41-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="bec41-196">Les `NavLink` attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="bec41-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="bec41-197">Dans l’exemple suivant, le `NavLink` composant comprend l' `target` attribut :</span><span class="sxs-lookup"><span data-stu-id="bec41-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="bec41-198">Le balisage HTML suivant est rendu :</span><span class="sxs-lookup"><span data-stu-id="bec41-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="bec41-199">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="bec41-199">URI and navigation state helpers</span></span>

<span data-ttu-id="bec41-200">Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les URI et la navigation dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="bec41-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="bec41-201">`NavigationManager`fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="bec41-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="bec41-202">Membre</span><span class="sxs-lookup"><span data-stu-id="bec41-202">Member</span></span> | <span data-ttu-id="bec41-203">Description</span><span class="sxs-lookup"><span data-stu-id="bec41-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="bec41-204">Uri</span><span class="sxs-lookup"><span data-stu-id="bec41-204">Uri</span></span> | <span data-ttu-id="bec41-205">Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="bec41-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="bec41-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="bec41-206">BaseUri</span></span> | <span data-ttu-id="bec41-207">Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="bec41-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="bec41-208">En général `BaseUri` , correspond à `href` l’attribut sur l’élément `<base>` du document *wwwroot/index.html* dans wwwroot/index.htmlBlazor (webassembly) ou *pages/_Host. cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="bec41-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="bec41-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="bec41-209">NavigateTo</span></span> | <span data-ttu-id="bec41-210">Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="bec41-210">Navigates to the specified URI.</span></span> <span data-ttu-id="bec41-211">Si `forceLoad` est `true`:</span><span class="sxs-lookup"><span data-stu-id="bec41-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="bec41-212">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="bec41-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="bec41-213">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="bec41-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="bec41-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="bec41-214">LocationChanged</span></span> | <span data-ttu-id="bec41-215">Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="bec41-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="bec41-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="bec41-216">ToAbsoluteUri</span></span> | <span data-ttu-id="bec41-217">Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="bec41-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="bec41-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="bec41-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="bec41-219">À partir d’un URI de base (par exemple, un URI `GetBaseUri`précédemment retourné par), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="bec41-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="bec41-220">Le composant suivant accède au composant de `Counter` l’application lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="bec41-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="bec41-221">Le composant suivant gère un événement de modification d’emplacement.</span><span class="sxs-lookup"><span data-stu-id="bec41-221">The following component handles a location changed event.</span></span> <span data-ttu-id="bec41-222">La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="bec41-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="bec41-223">Le déraccordement de la méthode permet de garbage collection du composant.</span><span class="sxs-lookup"><span data-stu-id="bec41-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="bec41-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fournit les informations suivantes sur l’événement :</span><span class="sxs-lookup"><span data-stu-id="bec41-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="bec41-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="bec41-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="bec41-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Si `true`, Blazor a intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="bec41-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="bec41-227">Si `false`la valeur est, [NavigationManager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) a entraîné la navigation.</span><span class="sxs-lookup"><span data-stu-id="bec41-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="bec41-228">Pour plus d’informations sur la suppression de <xref:blazor/lifecycle#component-disposal-with-idisposable>composants, consultez.</span><span class="sxs-lookup"><span data-stu-id="bec41-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
