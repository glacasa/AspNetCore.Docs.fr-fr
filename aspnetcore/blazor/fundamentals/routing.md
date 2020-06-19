---
title: Routage de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment acheminer des requêtes dans des applications et sur le composant NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 9668077d9b59ff20b1aab0b496278f2460e5ad2a
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103709"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="9ad8b-103">Routage de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9ad8b-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="9ad8b-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9ad8b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9ad8b-105">Découvrez comment acheminer les demandes et comment utiliser le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant pour créer des liens de navigation dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="9ad8b-106">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ad8b-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="9ad8b-107">Le serveur est intégré à [ASP.net Core routage des points de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="9ad8b-108">Une application ASP.NET Core est configurée pour accepter les connexions entrantes pour les composants interactifs avec <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="9ad8b-109">La configuration la plus courante consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="9ad8b-110">Par Convention, la page *hôte* est généralement nommée *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="9ad8b-111">L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="9ad8b-112">L’itinéraire de secours est pris en compte lorsque les autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="9ad8b-113">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l' Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="9ad8b-114">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="9ad8b-114">Route templates</span></span>

<span data-ttu-id="9ad8b-115">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant active le routage vers chaque composant avec un itinéraire spécifié.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="9ad8b-116">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant apparaît dans le fichier *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="9ad8b-117">Lorsqu’un fichier *. Razor* avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="9ad8b-118">Au moment de l’exécution, le <xref:Microsoft.AspNetCore.Components.RouteView> composant :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="9ad8b-119">Reçoit du avec les <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> paramètres souhaités.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="9ad8b-120">Restitue le composant spécifié avec sa disposition (ou une disposition par défaut facultative) à l’aide des paramètres spécifiés.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="9ad8b-121">Vous pouvez éventuellement spécifier un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> paramètre avec une classe de disposition à utiliser pour les composants qui ne spécifient pas de disposition.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="9ad8b-122">Les modèles par défaut Blazor spécifient le `MainLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="9ad8b-123">*MainLayout. Razor* se trouve dans le dossier *partagé* du projet de modèle.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="9ad8b-124">Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="9ad8b-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="9ad8b-125">Plusieurs modèles de routage peuvent être appliqués à un composant.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="9ad8b-126">Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="9ad8b-127">Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son fichier *wwwroot/index.html* ( Blazor webassembly) ou le fichier *pages/_Host. cshtml* ( Blazor serveur) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="9ad8b-128">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="9ad8b-129">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="9ad8b-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="9ad8b-130">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="9ad8b-131">Dans le fichier *app. Razor* , définissez le contenu personnalisé dans le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> paramètre de modèle du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-131">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="9ad8b-132">Le contenu des `<NotFound>` balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="9ad8b-133">Pour appliquer une disposition par défaut au <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="9ad8b-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="9ad8b-134">Acheminer vers des composants à partir de plusieurs assemblys</span><span class="sxs-lookup"><span data-stu-id="9ad8b-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="9ad8b-135">Utilisez le <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> paramètre pour spécifier des assemblys supplémentaires <xref:Microsoft.AspNetCore.Components.Routing.Router> que le composant doit prendre en compte lors de la recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="9ad8b-136">Les assemblys spécifiés sont pris en compte en plus de l' `AppAssembly` assembly spécifié.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="9ad8b-137">Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de classes référencée.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="9ad8b-138">L' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> exemple suivant entraîne la prise en charge du routage pour `Component1` :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="9ad8b-139">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="9ad8b-139">Route parameters</span></span>

<span data-ttu-id="9ad8b-140">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse) :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="9ad8b-141">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="9ad8b-142">Deux `@page` directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="9ad8b-143">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="9ad8b-144">La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="9ad8b-145">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="9ad8b-145">Route constraints</span></span>

<span data-ttu-id="9ad8b-146">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="9ad8b-147">Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="9ad8b-148">Un `Id` segment de routage est présent sur l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="9ad8b-149">Le `Id` segment est un entier ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="9ad8b-150">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="9ad8b-151">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="9ad8b-152">Contrainte</span><span class="sxs-lookup"><span data-stu-id="9ad8b-152">Constraint</span></span> | <span data-ttu-id="9ad8b-153">Exemple</span><span class="sxs-lookup"><span data-stu-id="9ad8b-153">Example</span></span>           | <span data-ttu-id="9ad8b-154">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="9ad8b-154">Example Matches</span></span>                                                                  | <span data-ttu-id="9ad8b-155">Invariant</span><span class="sxs-lookup"><span data-stu-id="9ad8b-155">Invariant</span></span><br><span data-ttu-id="9ad8b-156">culture</span><span class="sxs-lookup"><span data-stu-id="9ad8b-156">culture</span></span><br><span data-ttu-id="9ad8b-157">correspondance</span><span class="sxs-lookup"><span data-stu-id="9ad8b-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="9ad8b-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="9ad8b-159">Non</span><span class="sxs-lookup"><span data-stu-id="9ad8b-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="9ad8b-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="9ad8b-161">Oui</span><span class="sxs-lookup"><span data-stu-id="9ad8b-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="9ad8b-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="9ad8b-163">Oui</span><span class="sxs-lookup"><span data-stu-id="9ad8b-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="9ad8b-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9ad8b-165">Oui</span><span class="sxs-lookup"><span data-stu-id="9ad8b-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="9ad8b-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9ad8b-167">Oui</span><span class="sxs-lookup"><span data-stu-id="9ad8b-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="9ad8b-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="9ad8b-169">Non</span><span class="sxs-lookup"><span data-stu-id="9ad8b-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="9ad8b-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9ad8b-171">Oui</span><span class="sxs-lookup"><span data-stu-id="9ad8b-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="9ad8b-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9ad8b-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9ad8b-173">Oui</span><span class="sxs-lookup"><span data-stu-id="9ad8b-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="9ad8b-174">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou <xref:System.DateTime>) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="9ad8b-175">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="9ad8b-176">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="9ad8b-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="9ad8b-177">Dans Blazor les applications serveur, l’itinéraire par défaut dans *_Host. cshtml* est `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="9ad8b-178">Une URL de demande qui contient un point ( `.` ) ne correspond pas à l’itinéraire par défaut, car l’URL semble demander un fichier.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="9ad8b-179">Une Blazor application renvoie une réponse *404-introuvable* pour un fichier statique qui n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="9ad8b-180">Pour utiliser des itinéraires qui contiennent un point, configurez *_Host. cshtml* avec le modèle d’itinéraire suivant :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="9ad8b-181">Le `"/{**path}"` modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="9ad8b-182">Double-astérisque syntaxe *catch-all* ( `**` ) pour capturer le chemin d’accès dans plusieurs limites de dossiers sans encodage des barres obliques ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="9ad8b-183">`path`nom du paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="9ad8b-184">La syntaxe de paramètre *catch-all* ( `*` / `**` ) n’est **pas** prise en charge dans les Razor composants (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="9ad8b-185">Pour plus d’informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="9ad8b-186">Composant NavLink</span><span class="sxs-lookup"><span data-stu-id="9ad8b-186">NavLink component</span></span>

<span data-ttu-id="9ad8b-187">Utilisez un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="9ad8b-188">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="9ad8b-189">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="9ad8b-190">Le `NavMenu` composant suivant crée une barre de navigation de [démarrage](https://getbootstrap.com/docs/) qui montre comment utiliser des <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composants :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="9ad8b-191">Il existe deux <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-191">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="9ad8b-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à la totalité de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="9ad8b-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*valeur par défaut*) : le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="9ad8b-194">Dans l’exemple précédent, la page <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-194">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="9ad8b-195">Le deuxième <xref:Microsoft.AspNetCore.Components.Routing.NavLink> reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-195">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="9ad8b-196"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-196">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="9ad8b-197">Dans l’exemple suivant, le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant comprend l' `target` attribut :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-197">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="9ad8b-198">Le balisage HTML suivant est rendu :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="9ad8b-199">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="9ad8b-199">URI and navigation state helpers</span></span>

<span data-ttu-id="9ad8b-200">Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les URI et la navigation dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="9ad8b-201"><xref:Microsoft.AspNetCore.Components.NavigationManager>fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="9ad8b-202">Membre</span><span class="sxs-lookup"><span data-stu-id="9ad8b-202">Member</span></span> | <span data-ttu-id="9ad8b-203">Description</span><span class="sxs-lookup"><span data-stu-id="9ad8b-203">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="9ad8b-204">Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-204">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="9ad8b-205">Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="9ad8b-206">En général, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> correspond à l' `href` attribut sur l’élément du document `<base>` dans *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="9ad8b-206">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="9ad8b-207">Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-207">Navigates to the specified URI.</span></span> <span data-ttu-id="9ad8b-208">Si `forceLoad` est `true` :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="9ad8b-209">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="9ad8b-210">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="9ad8b-211">Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-211">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="9ad8b-212">Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-212">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="9ad8b-213">À partir d’un URI de base (par exemple, un URI précédemment retourné par <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-213">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="9ad8b-214">Le composant suivant accède au composant de l’application `Counter` lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="9ad8b-215">Le composant suivant gère un événement de modification d’emplacement en définissant <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="9ad8b-215">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9ad8b-216">La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-216">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="9ad8b-217">Le déraccordement de la méthode permet de garbage collection du composant.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-217">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="9ad8b-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fournit les informations suivantes sur l’événement :</span><span class="sxs-lookup"><span data-stu-id="9ad8b-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="9ad8b-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="9ad8b-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Si `true` , a Blazor intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="9ad8b-221">Si `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> la valeur est, la navigation a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="9ad8b-221">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="9ad8b-222">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="9ad8b-222">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
