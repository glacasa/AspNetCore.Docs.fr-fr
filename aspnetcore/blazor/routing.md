---
title: itinéraire ASP.NET Blazor Core
author: guardrex
description: Découvrez comment acheminer les demandes dans les applications et sur le composant NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218893"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="741e3-103">ASP.NET itinéraire Core Blazor</span><span class="sxs-lookup"><span data-stu-id="741e3-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="741e3-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="741e3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="741e3-105">Découvrez comment acheminer les `NavLink` demandes et comment utiliser le composant pour créer des liens de navigation dans les applications Blazor.</span><span class="sxs-lookup"><span data-stu-id="741e3-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="741e3-106">ASP.NET’intégration de l’itinéraire de point de terminaison de base</span><span class="sxs-lookup"><span data-stu-id="741e3-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="741e3-107">Blazor Server est intégré dans [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="741e3-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="741e3-108">Une application ASP.NET Core est configurée pour accepter les `MapBlazorHub` `Startup.Configure`connexions entrantes pour les composants interactifs avec :</span><span class="sxs-lookup"><span data-stu-id="741e3-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="741e3-109">La configuration la plus typique est d’acheminer toutes les demandes vers une page Razor, qui agit comme l’hôte de la partie côté serveur de l’application Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="741e3-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="741e3-110">Par convention, la page *d’accueil* est généralement nommée *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="741e3-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="741e3-111">L’itinéraire spécifié dans le fichier hôte est appelé une *voie de repli* parce qu’il fonctionne avec une faible priorité dans l’appariement des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="741e3-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="741e3-112">La route de repli est envisagée lorsque d’autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="741e3-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="741e3-113">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l’application Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="741e3-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="741e3-114">Modèles d’itinéraires</span><span class="sxs-lookup"><span data-stu-id="741e3-114">Route templates</span></span>

<span data-ttu-id="741e3-115">Le `Router` composant permet le routage vers chaque composant avec un itinéraire spécifié.</span><span class="sxs-lookup"><span data-stu-id="741e3-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="741e3-116">Le `Router` composant apparaît dans le fichier *App.razor* :</span><span class="sxs-lookup"><span data-stu-id="741e3-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="741e3-117">Lorsqu’un fichier *.razor* avec une `@page` directive est <xref:Microsoft.AspNetCore.Components.RouteAttribute> compilé, la classe générée est fournie un spécifier le modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="741e3-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="741e3-118">Au moment de `RouteView` l’exécution, le composant :</span><span class="sxs-lookup"><span data-stu-id="741e3-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="741e3-119">Reçoit `RouteData` le `Router` de la avec tous les paramètres souhaités.</span><span class="sxs-lookup"><span data-stu-id="741e3-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="741e3-120">Rend le composant spécifié avec sa mise en page (ou une mise en page par défaut facultative) à l’aide des paramètres spécifiés.</span><span class="sxs-lookup"><span data-stu-id="741e3-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="741e3-121">Vous pouvez spécifier en option un `DefaultLayout` paramètre avec une classe de mise en page à utiliser pour les composants qui ne spécifient pas une mise en page.</span><span class="sxs-lookup"><span data-stu-id="741e3-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="741e3-122">Les modèles Blazor par `MainLayout` défaut spécifient le composant.</span><span class="sxs-lookup"><span data-stu-id="741e3-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="741e3-123">*MainLayout.razor* se trouve dans le dossier *partagé* du projet de modèle.</span><span class="sxs-lookup"><span data-stu-id="741e3-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="741e3-124">Pour plus d’informations <xref:blazor/layouts>sur les mises en page, voir .</span><span class="sxs-lookup"><span data-stu-id="741e3-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="741e3-125">Plusieurs modèles d’itinéraire peuvent être appliqués à un composant.</span><span class="sxs-lookup"><span data-stu-id="741e3-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="741e3-126">Le composant suivant répond `/BlazorRoute` aux `/DifferentBlazorRoute`demandes et :</span><span class="sxs-lookup"><span data-stu-id="741e3-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="741e3-127">Pour que les URL se résolvent `<base>` correctement, l’application doit inclure une balise dans son fichier *wwwroot/index.html* (Blazor WebAssembly) ou *Pages/_Host.cshtml* fichier (Blazor Server) avec le chemin de base de l’application spécifié dans l’attribut `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="741e3-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="741e3-128">Pour plus d’informations, consultez <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="741e3-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="741e3-129">Fournir du contenu personnalisé lorsque le contenu n’est pas trouvé</span><span class="sxs-lookup"><span data-stu-id="741e3-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="741e3-130">Le `Router` composant permet à l’application de spécifier du contenu personnalisé si le contenu n’est pas trouvé pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="741e3-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="741e3-131">Dans le fichier *App.razor,* définissez du contenu personnalisé dans le `NotFound` paramètre du modèle du `Router` composant :</span><span class="sxs-lookup"><span data-stu-id="741e3-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="741e3-132">Le contenu `<NotFound>` des balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="741e3-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="741e3-133">Pour appliquer une `NotFound` mise en <xref:blazor/layouts>page par défaut sur le contenu, voir .</span><span class="sxs-lookup"><span data-stu-id="741e3-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="741e3-134">Itinéraire vers les composants de plusieurs assemblages</span><span class="sxs-lookup"><span data-stu-id="741e3-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="741e3-135">Utilisez `AdditionalAssemblies` le paramètre pour spécifier des assemblages supplémentaires pour le composant à considérer lors de la `Router` recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="741e3-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="741e3-136">Des assemblages spécifiés sont `AppAssembly`considérés en plus de l’assemblage spécifié.</span><span class="sxs-lookup"><span data-stu-id="741e3-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="741e3-137">Dans l’exemple `Component1` suivant, est un composant routable défini dans une bibliothèque de classe référencée.</span><span class="sxs-lookup"><span data-stu-id="741e3-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="741e3-138">L’exemple suivant `AdditionalAssemblies` se traduit `Component1`par un soutien de routage pour :</span><span class="sxs-lookup"><span data-stu-id="741e3-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="741e3-139">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="741e3-139">Route parameters</span></span>

<span data-ttu-id="741e3-140">Le routeur utilise les paramètres d’itinéraire pour remplir les paramètres de composant correspondants du même nom (insensible au cas) :</span><span class="sxs-lookup"><span data-stu-id="741e3-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="741e3-141">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="741e3-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="741e3-142">Deux `@page` directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="741e3-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="741e3-143">Le premier permet la navigation sur le composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="741e3-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="741e3-144">La `@page` deuxième directive `{text}` prend le paramètre d’itinéraire et attribue la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="741e3-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="741e3-145">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="741e3-145">Route constraints</span></span>

<span data-ttu-id="741e3-146">Une contrainte d’itinéraire applique l’appariement de type sur un segment d’itinéraire à un composant.</span><span class="sxs-lookup"><span data-stu-id="741e3-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="741e3-147">Dans l’exemple suivant, `Users` l’itinéraire vers le composant ne correspond que si :</span><span class="sxs-lookup"><span data-stu-id="741e3-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="741e3-148">Un `Id` segment d’itinéraire est présent sur l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="741e3-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="741e3-149">Le `Id` segment est un`int`intégrant ( ).</span><span class="sxs-lookup"><span data-stu-id="741e3-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="741e3-150">Les contraintes d’itinéraire indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="741e3-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="741e3-151">Pour les contraintes d’itinéraire qui correspondent à la culture invariante, voir l’avertissement ci-dessous le tableau pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="741e3-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="741e3-152">Contrainte</span><span class="sxs-lookup"><span data-stu-id="741e3-152">Constraint</span></span> | <span data-ttu-id="741e3-153">Exemple</span><span class="sxs-lookup"><span data-stu-id="741e3-153">Example</span></span>           | <span data-ttu-id="741e3-154">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="741e3-154">Example Matches</span></span>                                                                  | <span data-ttu-id="741e3-155">Invariant</span><span class="sxs-lookup"><span data-stu-id="741e3-155">Invariant</span></span><br><span data-ttu-id="741e3-156">culture</span><span class="sxs-lookup"><span data-stu-id="741e3-156">culture</span></span><br><span data-ttu-id="741e3-157">correspondance</span><span class="sxs-lookup"><span data-stu-id="741e3-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="741e3-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="741e3-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="741e3-159">Non</span><span class="sxs-lookup"><span data-stu-id="741e3-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="741e3-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="741e3-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="741e3-161">Oui</span><span class="sxs-lookup"><span data-stu-id="741e3-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="741e3-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="741e3-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="741e3-163">Oui</span><span class="sxs-lookup"><span data-stu-id="741e3-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="741e3-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="741e3-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="741e3-165">Oui</span><span class="sxs-lookup"><span data-stu-id="741e3-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="741e3-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="741e3-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="741e3-167">Oui</span><span class="sxs-lookup"><span data-stu-id="741e3-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="741e3-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="741e3-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="741e3-169">Non</span><span class="sxs-lookup"><span data-stu-id="741e3-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="741e3-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="741e3-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="741e3-171">Oui</span><span class="sxs-lookup"><span data-stu-id="741e3-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="741e3-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="741e3-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="741e3-173">Oui</span><span class="sxs-lookup"><span data-stu-id="741e3-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="741e3-174">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="741e3-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="741e3-175">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="741e3-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="741e3-176">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="741e3-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="741e3-177">Dans les applications Blazor Server, l’itinéraire par défaut `/` dans`@page "/"` *_Host.cshtml* est ( ).</span><span class="sxs-lookup"><span data-stu-id="741e3-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="741e3-178">Une URL de demande`.`qui contient un point ( ) n’est pas égalée par l’itinéraire par défaut parce que l’URL semble demander un fichier.</span><span class="sxs-lookup"><span data-stu-id="741e3-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="741e3-179">Une application Blazor renvoie une réponse *404 - Non trouvée* pour un fichier statique qui n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="741e3-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="741e3-180">Pour utiliser les itinéraires qui contiennent un point, configurez *_Host.cshtml* avec le modèle d’itinéraire suivant :</span><span class="sxs-lookup"><span data-stu-id="741e3-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="741e3-181">Le `"/{**path}"` modèle comprend :</span><span class="sxs-lookup"><span data-stu-id="741e3-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="741e3-182">Syntaxe *fourre-tout* double-astérisque (`**`) pour capturer le chemin à`/`travers les limites multiples de dossier sans codage vers l’avant slashes ( ).</span><span class="sxs-lookup"><span data-stu-id="741e3-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="741e3-183">`path`nom du paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="741e3-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="741e3-184">La syntaxe de`*`/`**`paramètre *fourre-tout* ( ) **n’est pas** prise en charge dans les composants Razor (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="741e3-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="741e3-185">Pour plus d’informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="741e3-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="741e3-186">Composant NavLink</span><span class="sxs-lookup"><span data-stu-id="741e3-186">NavLink component</span></span>

<span data-ttu-id="741e3-187">Utilisez `NavLink` un composant à la place`<a>`d’éléments hyperliens HTML ( ) lors de la création de liaisons de navigation.</span><span class="sxs-lookup"><span data-stu-id="741e3-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="741e3-188">Un `NavLink` composant se `<a>` comporte comme un élément, `active` sauf qu’il bascule une classe CSS en fonction de si son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="741e3-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="741e3-189">La `active` classe aide un utilisateur à comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="741e3-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="741e3-190">Le `NavMenu` composant suivant crée une barre de navigation `NavLink` [Bootstrap](https://getbootstrap.com/docs/) qui démontre comment utiliser les composants :</span><span class="sxs-lookup"><span data-stu-id="741e3-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="741e3-191">Il existe `NavLinkMatch` deux options que `Match` vous pouvez `<NavLink>` attribuer à l’attribut de l’élément :</span><span class="sxs-lookup"><span data-stu-id="741e3-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="741e3-192">`NavLinkMatch.All`&ndash; Le `NavLink` est actif quand il correspond à l’ENSEMBLE de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="741e3-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="741e3-193">`NavLinkMatch.Prefix`(*par défaut*) &ndash; Le `NavLink` est actif quand il correspond à n’importe quel préfixe de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="741e3-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="741e3-194">Dans l’exemple précédent, la Maison correspond à `NavLink` `href=""` l’URL à domicile et ne reçoit `active` la `https://localhost:5001/`classe CSS que sur l’URL de trajectoire de base par défaut de l’application (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="741e3-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="741e3-195">Le `NavLink` second `active` reçoit la classe lorsque l’utilisateur visite n’importe quelle URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="741e3-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="741e3-196">D’autres attributs de `NavLink` composants sont transmis à l’étiquette d’ancrage rendu.</span><span class="sxs-lookup"><span data-stu-id="741e3-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="741e3-197">Dans l’exemple `NavLink` suivant, `target` le composant comprend l’attribut :</span><span class="sxs-lookup"><span data-stu-id="741e3-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="741e3-198">La balisage HTML suivante est rendue :</span><span class="sxs-lookup"><span data-stu-id="741e3-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="741e3-199">Aides d’état de navigation et d’URI</span><span class="sxs-lookup"><span data-stu-id="741e3-199">URI and navigation state helpers</span></span>

<span data-ttu-id="741e3-200">Utilisez-le <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les ITI et la navigation dans le code C.</span><span class="sxs-lookup"><span data-stu-id="741e3-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="741e3-201">`NavigationManager`fournit l’événement et les méthodes indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="741e3-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="741e3-202">Membre</span><span class="sxs-lookup"><span data-stu-id="741e3-202">Member</span></span> | <span data-ttu-id="741e3-203">Description</span><span class="sxs-lookup"><span data-stu-id="741e3-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="741e3-204">Uri</span><span class="sxs-lookup"><span data-stu-id="741e3-204">Uri</span></span> | <span data-ttu-id="741e3-205">Obtient l’URI absolue actuelle.</span><span class="sxs-lookup"><span data-stu-id="741e3-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="741e3-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="741e3-206">BaseUri</span></span> | <span data-ttu-id="741e3-207">Obtient la base URI (avec une barre oblique de fuite) qui peut être prépendi à des chemins relatifs URI pour produire une URI absolue.</span><span class="sxs-lookup"><span data-stu-id="741e3-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="741e3-208">En `BaseUri` règle générale, `href` correspond à l’attribut sur l’élément `<base>` du document dans *wwwroot/index.html* (WebAssembly)Blazor ou *Pages/_Host.cshtml* (Serveur).Blazor</span><span class="sxs-lookup"><span data-stu-id="741e3-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="741e3-209">NaviguerTo</span><span class="sxs-lookup"><span data-stu-id="741e3-209">NavigateTo</span></span> | <span data-ttu-id="741e3-210">Navigue vers l’URI spécifiée.</span><span class="sxs-lookup"><span data-stu-id="741e3-210">Navigates to the specified URI.</span></span> <span data-ttu-id="741e3-211">Si `forceLoad` `true`c’est :</span><span class="sxs-lookup"><span data-stu-id="741e3-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="741e3-212">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="741e3-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="741e3-213">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="741e3-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="741e3-214">Emplacement Modifié</span><span class="sxs-lookup"><span data-stu-id="741e3-214">LocationChanged</span></span> | <span data-ttu-id="741e3-215">Un événement qui s’agite lorsque l’emplacement de la navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="741e3-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="741e3-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="741e3-216">ToAbsoluteUri</span></span> | <span data-ttu-id="741e3-217">Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="741e3-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="741e3-218"><span style="word-break:normal;word-wrap:normal">ÀBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="741e3-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="741e3-219">Compte tenu d’une base URI (par `GetBaseUri`exemple, un URI précédemment retourné par ), convertit un URI absolu en un URI par rapport au préfixe de base URI.</span><span class="sxs-lookup"><span data-stu-id="741e3-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="741e3-220">Le composant suivant navigue vers `Counter` le composant de l’application lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="741e3-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="741e3-221">Le composant suivant gère un événement modifié d’emplacement.</span><span class="sxs-lookup"><span data-stu-id="741e3-221">The following component handles a location changed event.</span></span> <span data-ttu-id="741e3-222">La `HandleLocationChanged` méthode est décomookée lorsqu’elle `Dispose` est appelée par le cadre.</span><span class="sxs-lookup"><span data-stu-id="741e3-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="741e3-223">Le fait de débrancher la méthode permet la collecte des ordures du composant.</span><span class="sxs-lookup"><span data-stu-id="741e3-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
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

<span data-ttu-id="741e3-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fournit les informations suivantes sur l’événement :</span><span class="sxs-lookup"><span data-stu-id="741e3-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="741e3-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; L’URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="741e3-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="741e3-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Si `true` Blazor , intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="741e3-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="741e3-227">Si `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) a causé la navigation de se produire.</span><span class="sxs-lookup"><span data-stu-id="741e3-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="741e3-228">Pour plus d’informations <xref:blazor/lifecycle#component-disposal-with-idisposable>sur l’élimination des composants, voir .</span><span class="sxs-lookup"><span data-stu-id="741e3-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
