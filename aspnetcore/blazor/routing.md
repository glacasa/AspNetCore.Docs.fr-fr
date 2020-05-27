---
<span data-ttu-id="98e44-101">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-102">'Blazor'</span></span>
- <span data-ttu-id="98e44-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-103">'Identity'</span></span>
- <span data-ttu-id="98e44-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-105">'Razor'</span></span>
- <span data-ttu-id="98e44-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="98e44-107">Routage de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="98e44-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="98e44-108">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98e44-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="98e44-109">Découvrez comment acheminer les demandes et comment utiliser le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant pour créer des liens de navigation dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="98e44-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="98e44-110">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98e44-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="98e44-111">Le serveur est intégré à [ASP.net Core routage des points de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="98e44-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="98e44-112">Une application ASP.NET Core est configurée pour accepter les connexions entrantes pour les composants interactifs avec <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="98e44-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="98e44-113">La configuration la plus courante consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="98e44-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="98e44-114">Par Convention, la page *hôte* est généralement nommée *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="98e44-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="98e44-115">L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="98e44-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="98e44-116">L’itinéraire de secours est pris en compte lorsque les autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="98e44-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="98e44-117">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l' Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="98e44-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="98e44-118">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="98e44-118">Route templates</span></span>

<span data-ttu-id="98e44-119">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant active le routage vers chaque composant avec un itinéraire spécifié.</span><span class="sxs-lookup"><span data-stu-id="98e44-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="98e44-120">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant apparaît dans le fichier *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="98e44-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="98e44-121">Lorsqu’un fichier *. Razor* avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="98e44-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="98e44-122">Au moment de l’exécution, le <xref:Microsoft.AspNetCore.Components.RouteView> composant :</span><span class="sxs-lookup"><span data-stu-id="98e44-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="98e44-123">Reçoit du avec les <xref:Microsoft.AspNetCore.Components.RouteData> <xref:Microsoft.AspNetCore.Components.Routing.Router> paramètres souhaités.</span><span class="sxs-lookup"><span data-stu-id="98e44-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="98e44-124">Restitue le composant spécifié avec sa disposition (ou une disposition par défaut facultative) à l’aide des paramètres spécifiés.</span><span class="sxs-lookup"><span data-stu-id="98e44-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="98e44-125">Vous pouvez éventuellement spécifier un <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> paramètre avec une classe de disposition à utiliser pour les composants qui ne spécifient pas de disposition.</span><span class="sxs-lookup"><span data-stu-id="98e44-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="98e44-126">Les modèles par défaut Blazor spécifient le `MainLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="98e44-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="98e44-127">*MainLayout. Razor* se trouve dans le dossier *partagé* du projet de modèle.</span><span class="sxs-lookup"><span data-stu-id="98e44-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="98e44-128">Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="98e44-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="98e44-129">Plusieurs modèles de routage peuvent être appliqués à un composant.</span><span class="sxs-lookup"><span data-stu-id="98e44-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="98e44-130">Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="98e44-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="98e44-131">Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son fichier *wwwroot/index.html* ( Blazor webassembly) ou le fichier *pages/_Host. cshtml* ( Blazor serveur) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="98e44-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="98e44-132">Pour plus d'informations, consultez <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="98e44-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="98e44-133">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="98e44-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="98e44-134">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="98e44-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="98e44-135">Dans le fichier *app. Razor* , définissez le contenu personnalisé dans le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> paramètre de modèle du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant :</span><span class="sxs-lookup"><span data-stu-id="98e44-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="98e44-136">Le contenu des `<NotFound>` balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="98e44-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="98e44-137">Pour appliquer une disposition par défaut au <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="98e44-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="98e44-138">Acheminer vers des composants à partir de plusieurs assemblys</span><span class="sxs-lookup"><span data-stu-id="98e44-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="98e44-139">Utilisez le <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> paramètre pour spécifier des assemblys supplémentaires <xref:Microsoft.AspNetCore.Components.Routing.Router> que le composant doit prendre en compte lors de la recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="98e44-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="98e44-140">Les assemblys spécifiés sont pris en compte en plus de l' `AppAssembly` assembly spécifié.</span><span class="sxs-lookup"><span data-stu-id="98e44-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="98e44-141">Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de classes référencée.</span><span class="sxs-lookup"><span data-stu-id="98e44-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="98e44-142">L' <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> exemple suivant entraîne la prise en charge du routage pour `Component1` :</span><span class="sxs-lookup"><span data-stu-id="98e44-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="98e44-143">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="98e44-143">Route parameters</span></span>

<span data-ttu-id="98e44-144">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse) :</span><span class="sxs-lookup"><span data-stu-id="98e44-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="98e44-145">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="98e44-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="98e44-146">Deux `@page` directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="98e44-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="98e44-147">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="98e44-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="98e44-148">La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="98e44-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="98e44-149">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="98e44-149">Route constraints</span></span>

<span data-ttu-id="98e44-150">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="98e44-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="98e44-151">Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si :</span><span class="sxs-lookup"><span data-stu-id="98e44-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="98e44-152">Un `Id` segment de routage est présent sur l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="98e44-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="98e44-153">Le `Id` segment est un entier ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="98e44-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="98e44-154">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="98e44-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="98e44-155">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="98e44-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="98e44-156">Contrainte</span><span class="sxs-lookup"><span data-stu-id="98e44-156">Constraint</span></span> | <span data-ttu-id="98e44-157">Exemple</span><span class="sxs-lookup"><span data-stu-id="98e44-157">Example</span></span>           | <span data-ttu-id="98e44-158">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="98e44-158">Example Matches</span></span>                                                                  | <span data-ttu-id="98e44-159">Invariant</span><span class="sxs-lookup"><span data-stu-id="98e44-159">Invariant</span></span><br><span data-ttu-id="98e44-160">culture</span><span class="sxs-lookup"><span data-stu-id="98e44-160">culture</span></span><br><span data-ttu-id="98e44-161">correspondance</span><span class="sxs-lookup"><span data-stu-id="98e44-161">matching</span></span> |
| ---
<span data-ttu-id="98e44-162">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-163">'Blazor'</span></span>
- <span data-ttu-id="98e44-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-164">'Identity'</span></span>
- <span data-ttu-id="98e44-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-166">'Razor'</span></span>
- <span data-ttu-id="98e44-167">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-168">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-169">'Blazor'</span></span>
- <span data-ttu-id="98e44-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-170">'Identity'</span></span>
- <span data-ttu-id="98e44-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-172">'Razor'</span></span>
- <span data-ttu-id="98e44-173">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-174">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-175">'Blazor'</span></span>
- <span data-ttu-id="98e44-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-176">'Identity'</span></span>
- <span data-ttu-id="98e44-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-178">'Razor'</span></span>
- <span data-ttu-id="98e44-179">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-179">'SignalR' uid:</span></span> 

<span data-ttu-id="98e44-180">----- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-181">'Blazor'</span></span>
- <span data-ttu-id="98e44-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-182">'Identity'</span></span>
- <span data-ttu-id="98e44-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-184">'Razor'</span></span>
- <span data-ttu-id="98e44-185">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-186">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-187">'Blazor'</span></span>
- <span data-ttu-id="98e44-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-188">'Identity'</span></span>
- <span data-ttu-id="98e44-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-190">'Razor'</span></span>
- <span data-ttu-id="98e44-191">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-192">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-193">'Blazor'</span></span>
- <span data-ttu-id="98e44-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-194">'Identity'</span></span>
- <span data-ttu-id="98e44-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-196">'Razor'</span></span>
- <span data-ttu-id="98e44-197">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-198">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-199">'Blazor'</span></span>
- <span data-ttu-id="98e44-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-200">'Identity'</span></span>
- <span data-ttu-id="98e44-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-202">'Razor'</span></span>
- <span data-ttu-id="98e44-203">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-204">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-205">'Blazor'</span></span>
- <span data-ttu-id="98e44-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-206">'Identity'</span></span>
- <span data-ttu-id="98e44-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-208">'Razor'</span></span>
- <span data-ttu-id="98e44-209">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-210">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-211">'Blazor'</span></span>
- <span data-ttu-id="98e44-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-212">'Identity'</span></span>
- <span data-ttu-id="98e44-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-214">'Razor'</span></span>
- <span data-ttu-id="98e44-215">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-215">'SignalR' uid:</span></span> 

<span data-ttu-id="98e44-216">--------- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-217">'Blazor'</span></span>
- <span data-ttu-id="98e44-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-218">'Identity'</span></span>
- <span data-ttu-id="98e44-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-220">'Razor'</span></span>
- <span data-ttu-id="98e44-221">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-222">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-223">'Blazor'</span></span>
- <span data-ttu-id="98e44-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-224">'Identity'</span></span>
- <span data-ttu-id="98e44-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-226">'Razor'</span></span>
- <span data-ttu-id="98e44-227">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-228">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-229">'Blazor'</span></span>
- <span data-ttu-id="98e44-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-230">'Identity'</span></span>
- <span data-ttu-id="98e44-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-232">'Razor'</span></span>
- <span data-ttu-id="98e44-233">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-234">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-235">'Blazor'</span></span>
- <span data-ttu-id="98e44-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-236">'Identity'</span></span>
- <span data-ttu-id="98e44-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-238">'Razor'</span></span>
- <span data-ttu-id="98e44-239">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-240">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-241">'Blazor'</span></span>
- <span data-ttu-id="98e44-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-242">'Identity'</span></span>
- <span data-ttu-id="98e44-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-244">'Razor'</span></span>
- <span data-ttu-id="98e44-245">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-246">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-247">'Blazor'</span></span>
- <span data-ttu-id="98e44-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-248">'Identity'</span></span>
- <span data-ttu-id="98e44-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-250">'Razor'</span></span>
- <span data-ttu-id="98e44-251">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-252">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-253">'Blazor'</span></span>
- <span data-ttu-id="98e44-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-254">'Identity'</span></span>
- <span data-ttu-id="98e44-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-256">'Razor'</span></span>
- <span data-ttu-id="98e44-257">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-258">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-259">'Blazor'</span></span>
- <span data-ttu-id="98e44-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-260">'Identity'</span></span>
- <span data-ttu-id="98e44-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-262">'Razor'</span></span>
- <span data-ttu-id="98e44-263">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-264">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-265">'Blazor'</span></span>
- <span data-ttu-id="98e44-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-266">'Identity'</span></span>
- <span data-ttu-id="98e44-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-268">'Razor'</span></span>
- <span data-ttu-id="98e44-269">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-270">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-271">'Blazor'</span></span>
- <span data-ttu-id="98e44-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-272">'Identity'</span></span>
- <span data-ttu-id="98e44-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-274">'Razor'</span></span>
- <span data-ttu-id="98e44-275">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-276">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-277">'Blazor'</span></span>
- <span data-ttu-id="98e44-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-278">'Identity'</span></span>
- <span data-ttu-id="98e44-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-280">'Razor'</span></span>
- <span data-ttu-id="98e44-281">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-282">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-283">'Blazor'</span></span>
- <span data-ttu-id="98e44-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-284">'Identity'</span></span>
- <span data-ttu-id="98e44-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-286">'Razor'</span></span>
- <span data-ttu-id="98e44-287">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-288">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-289">'Blazor'</span></span>
- <span data-ttu-id="98e44-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-290">'Identity'</span></span>
- <span data-ttu-id="98e44-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-292">'Razor'</span></span>
- <span data-ttu-id="98e44-293">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-294">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-295">'Blazor'</span></span>
- <span data-ttu-id="98e44-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-296">'Identity'</span></span>
- <span data-ttu-id="98e44-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-298">'Razor'</span></span>
- <span data-ttu-id="98e44-299">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-300">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-301">'Blazor'</span></span>
- <span data-ttu-id="98e44-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-302">'Identity'</span></span>
- <span data-ttu-id="98e44-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-304">'Razor'</span></span>
- <span data-ttu-id="98e44-305">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-306">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-307">'Blazor'</span></span>
- <span data-ttu-id="98e44-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-308">'Identity'</span></span>
- <span data-ttu-id="98e44-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-310">'Razor'</span></span>
- <span data-ttu-id="98e44-311">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-312">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-313">'Blazor'</span></span>
- <span data-ttu-id="98e44-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-314">'Identity'</span></span>
- <span data-ttu-id="98e44-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-316">'Razor'</span></span>
- <span data-ttu-id="98e44-317">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-318">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-319">'Blazor'</span></span>
- <span data-ttu-id="98e44-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-320">'Identity'</span></span>
- <span data-ttu-id="98e44-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-322">'Razor'</span></span>
- <span data-ttu-id="98e44-323">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-324">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-325">'Blazor'</span></span>
- <span data-ttu-id="98e44-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-326">'Identity'</span></span>
- <span data-ttu-id="98e44-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-328">'Razor'</span></span>
- <span data-ttu-id="98e44-329">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-330">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-331">'Blazor'</span></span>
- <span data-ttu-id="98e44-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-332">'Identity'</span></span>
- <span data-ttu-id="98e44-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-334">'Razor'</span></span>
- <span data-ttu-id="98e44-335">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-336">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-337">'Blazor'</span></span>
- <span data-ttu-id="98e44-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-338">'Identity'</span></span>
- <span data-ttu-id="98e44-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-340">'Razor'</span></span>
- <span data-ttu-id="98e44-341">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-342">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-343">'Blazor'</span></span>
- <span data-ttu-id="98e44-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-344">'Identity'</span></span>
- <span data-ttu-id="98e44-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-346">'Razor'</span></span>
- <span data-ttu-id="98e44-347">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-348">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-349">'Blazor'</span></span>
- <span data-ttu-id="98e44-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-350">'Identity'</span></span>
- <span data-ttu-id="98e44-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-352">'Razor'</span></span>
- <span data-ttu-id="98e44-353">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-354">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-355">'Blazor'</span></span>
- <span data-ttu-id="98e44-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-356">'Identity'</span></span>
- <span data-ttu-id="98e44-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-358">'Razor'</span></span>
- <span data-ttu-id="98e44-359">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-360">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-361">'Blazor'</span></span>
- <span data-ttu-id="98e44-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-362">'Identity'</span></span>
- <span data-ttu-id="98e44-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-364">'Razor'</span></span>
- <span data-ttu-id="98e44-365">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-366">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-367">'Blazor'</span></span>
- <span data-ttu-id="98e44-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-368">'Identity'</span></span>
- <span data-ttu-id="98e44-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-370">'Razor'</span></span>
- <span data-ttu-id="98e44-371">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-372">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-373">'Blazor'</span></span>
- <span data-ttu-id="98e44-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-374">'Identity'</span></span>
- <span data-ttu-id="98e44-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-376">'Razor'</span></span>
- <span data-ttu-id="98e44-377">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-378">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-379">'Blazor'</span></span>
- <span data-ttu-id="98e44-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-380">'Identity'</span></span>
- <span data-ttu-id="98e44-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-382">'Razor'</span></span>
- <span data-ttu-id="98e44-383">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-384">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-385">'Blazor'</span></span>
- <span data-ttu-id="98e44-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-386">'Identity'</span></span>
- <span data-ttu-id="98e44-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-388">'Razor'</span></span>
- <span data-ttu-id="98e44-389">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-390">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-391">'Blazor'</span></span>
- <span data-ttu-id="98e44-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-392">'Identity'</span></span>
- <span data-ttu-id="98e44-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-394">'Razor'</span></span>
- <span data-ttu-id="98e44-395">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-396">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-397">'Blazor'</span></span>
- <span data-ttu-id="98e44-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-398">'Identity'</span></span>
- <span data-ttu-id="98e44-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-400">'Razor'</span></span>
- <span data-ttu-id="98e44-401">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-402">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-403">'Blazor'</span></span>
- <span data-ttu-id="98e44-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-404">'Identity'</span></span>
- <span data-ttu-id="98e44-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-406">'Razor'</span></span>
- <span data-ttu-id="98e44-407">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-408">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-409">'Blazor'</span></span>
- <span data-ttu-id="98e44-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-410">'Identity'</span></span>
- <span data-ttu-id="98e44-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-412">'Razor'</span></span>
- <span data-ttu-id="98e44-413">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-414">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-415">'Blazor'</span></span>
- <span data-ttu-id="98e44-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-416">'Identity'</span></span>
- <span data-ttu-id="98e44-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-418">'Razor'</span></span>
- <span data-ttu-id="98e44-419">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-420">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-421">'Blazor'</span></span>
- <span data-ttu-id="98e44-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-422">'Identity'</span></span>
- <span data-ttu-id="98e44-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-424">'Razor'</span></span>
- <span data-ttu-id="98e44-425">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-426">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-427">'Blazor'</span></span>
- <span data-ttu-id="98e44-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-428">'Identity'</span></span>
- <span data-ttu-id="98e44-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-430">'Razor'</span></span>
- <span data-ttu-id="98e44-431">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-432">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-433">'Blazor'</span></span>
- <span data-ttu-id="98e44-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-434">'Identity'</span></span>
- <span data-ttu-id="98e44-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-436">'Razor'</span></span>
- <span data-ttu-id="98e44-437">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-438">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-439">'Blazor'</span></span>
- <span data-ttu-id="98e44-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-440">'Identity'</span></span>
- <span data-ttu-id="98e44-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-442">'Razor'</span></span>
- <span data-ttu-id="98e44-443">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-443">'SignalR' uid:</span></span> 

<span data-ttu-id="98e44-444">---------------------------------------- | :---titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-445">'Blazor'</span></span>
- <span data-ttu-id="98e44-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-446">'Identity'</span></span>
- <span data-ttu-id="98e44-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-448">'Razor'</span></span>
- <span data-ttu-id="98e44-449">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-450">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-451">'Blazor'</span></span>
- <span data-ttu-id="98e44-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-452">'Identity'</span></span>
- <span data-ttu-id="98e44-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-454">'Razor'</span></span>
- <span data-ttu-id="98e44-455">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-456">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-457">'Blazor'</span></span>
- <span data-ttu-id="98e44-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-458">'Identity'</span></span>
- <span data-ttu-id="98e44-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-460">'Razor'</span></span>
- <span data-ttu-id="98e44-461">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-462">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-463">'Blazor'</span></span>
- <span data-ttu-id="98e44-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-464">'Identity'</span></span>
- <span data-ttu-id="98e44-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-466">'Razor'</span></span>
- <span data-ttu-id="98e44-467">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-468">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-469">'Blazor'</span></span>
- <span data-ttu-id="98e44-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-470">'Identity'</span></span>
- <span data-ttu-id="98e44-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-472">'Razor'</span></span>
- <span data-ttu-id="98e44-473">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-474">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-475">'Blazor'</span></span>
- <span data-ttu-id="98e44-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-476">'Identity'</span></span>
- <span data-ttu-id="98e44-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-478">'Razor'</span></span>
- <span data-ttu-id="98e44-479">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-480">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-481">'Blazor'</span></span>
- <span data-ttu-id="98e44-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-482">'Identity'</span></span>
- <span data-ttu-id="98e44-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-484">'Razor'</span></span>
- <span data-ttu-id="98e44-485">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-486">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-487">'Blazor'</span></span>
- <span data-ttu-id="98e44-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-488">'Identity'</span></span>
- <span data-ttu-id="98e44-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-490">'Razor'</span></span>
- <span data-ttu-id="98e44-491">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-492">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-493">'Blazor'</span></span>
- <span data-ttu-id="98e44-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-494">'Identity'</span></span>
- <span data-ttu-id="98e44-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-496">'Razor'</span></span>
- <span data-ttu-id="98e44-497">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-498">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-499">'Blazor'</span></span>
- <span data-ttu-id="98e44-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-500">'Identity'</span></span>
- <span data-ttu-id="98e44-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-502">'Razor'</span></span>
- <span data-ttu-id="98e44-503">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-504">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-505">'Blazor'</span></span>
- <span data-ttu-id="98e44-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-506">'Identity'</span></span>
- <span data-ttu-id="98e44-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-508">'Razor'</span></span>
- <span data-ttu-id="98e44-509">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-510">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-511">'Blazor'</span></span>
- <span data-ttu-id="98e44-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-512">'Identity'</span></span>
- <span data-ttu-id="98e44-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-514">'Razor'</span></span>
- <span data-ttu-id="98e44-515">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-516">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-517">'Blazor'</span></span>
- <span data-ttu-id="98e44-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-518">'Identity'</span></span>
- <span data-ttu-id="98e44-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-520">'Razor'</span></span>
- <span data-ttu-id="98e44-521">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-521">'SignalR' uid:</span></span> 

<span data-ttu-id="98e44-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Non | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Oui | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Oui | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Oui | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Oui | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Non | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Oui | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Oui |</span><span class="sxs-lookup"><span data-stu-id="98e44-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="98e44-523">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou <xref:System.DateTime>) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="98e44-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="98e44-524">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="98e44-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="98e44-525">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="98e44-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="98e44-526">Dans Blazor les applications serveur, l’itinéraire par défaut dans *_Host. cshtml* est `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="98e44-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="98e44-527">Une URL de demande qui contient un point ( `.` ) ne correspond pas à l’itinéraire par défaut, car l’URL semble demander un fichier.</span><span class="sxs-lookup"><span data-stu-id="98e44-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="98e44-528">Une Blazor application renvoie une réponse *404-introuvable* pour un fichier statique qui n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="98e44-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="98e44-529">Pour utiliser des itinéraires qui contiennent un point, configurez *_Host. cshtml* avec le modèle d’itinéraire suivant :</span><span class="sxs-lookup"><span data-stu-id="98e44-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="98e44-530">Le `"/{**path}"` modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="98e44-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="98e44-531">Double-astérisque syntaxe *catch-all* ( `**` ) pour capturer le chemin d’accès dans plusieurs limites de dossiers sans encodage des barres obliques ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="98e44-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="98e44-532">`path`nom du paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="98e44-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="98e44-533">La syntaxe de paramètre *catch-all* ( `*` / `**` ) n’est **pas** prise en charge dans les Razor composants (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="98e44-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="98e44-534">Pour plus d'informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="98e44-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="98e44-535">Composant NavLink</span><span class="sxs-lookup"><span data-stu-id="98e44-535">NavLink component</span></span>

<span data-ttu-id="98e44-536">Utilisez un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="98e44-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="98e44-537">Un <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="98e44-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="98e44-538">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="98e44-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="98e44-539">Le `NavMenu` composant suivant crée une barre de navigation de [démarrage](https://getbootstrap.com/docs/) qui montre comment utiliser des <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composants :</span><span class="sxs-lookup"><span data-stu-id="98e44-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="98e44-540">Il existe deux <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :</span><span class="sxs-lookup"><span data-stu-id="98e44-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="98e44-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>&ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Est actif lorsqu’il correspond à la totalité de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="98e44-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType> &ndash; The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="98e44-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(*par défaut*) &ndash; <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="98e44-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*) &ndash; The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="98e44-543">Dans l’exemple précédent, la page <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="98e44-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="98e44-544">Le deuxième <xref:Microsoft.AspNetCore.Components.Routing.NavLink> reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="98e44-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="98e44-545"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="98e44-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="98e44-546">Dans l’exemple suivant, le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant comprend l' `target` attribut :</span><span class="sxs-lookup"><span data-stu-id="98e44-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="98e44-547">Le balisage HTML suivant est rendu :</span><span class="sxs-lookup"><span data-stu-id="98e44-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="98e44-548">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="98e44-548">URI and navigation state helpers</span></span>

<span data-ttu-id="98e44-549">Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les URI et la navigation dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="98e44-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="98e44-550"><xref:Microsoft.AspNetCore.Components.NavigationManager>fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="98e44-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="98e44-551">Membre</span><span class="sxs-lookup"><span data-stu-id="98e44-551">Member</span></span> | <span data-ttu-id="98e44-552">Description</span><span class="sxs-lookup"><span data-stu-id="98e44-552">Description</span></span> |
| ---
<span data-ttu-id="98e44-553">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-554">'Blazor'</span></span>
- <span data-ttu-id="98e44-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-555">'Identity'</span></span>
- <span data-ttu-id="98e44-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-557">'Razor'</span></span>
- <span data-ttu-id="98e44-558">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-558">'SignalR' uid:</span></span> 

<span data-ttu-id="98e44-559">--- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-560">'Blazor'</span></span>
- <span data-ttu-id="98e44-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-561">'Identity'</span></span>
- <span data-ttu-id="98e44-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-563">'Razor'</span></span>
- <span data-ttu-id="98e44-564">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-565">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-566">'Blazor'</span></span>
- <span data-ttu-id="98e44-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-567">'Identity'</span></span>
- <span data-ttu-id="98e44-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-569">'Razor'</span></span>
- <span data-ttu-id="98e44-570">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="98e44-571">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="98e44-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98e44-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98e44-572">'Blazor'</span></span>
- <span data-ttu-id="98e44-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98e44-573">'Identity'</span></span>
- <span data-ttu-id="98e44-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98e44-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="98e44-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98e44-575">'Razor'</span></span>
- <span data-ttu-id="98e44-576">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="98e44-576">'SignalR' uid:</span></span> 

<span data-ttu-id="98e44-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="98e44-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="98e44-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="98e44-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="98e44-579">En général, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> correspond à l' `href` attribut sur l’élément du document `<base>` dans *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="98e44-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="98e44-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="98e44-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="98e44-581">Si `forceLoad` est `true` :</span><span class="sxs-lookup"><span data-stu-id="98e44-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="98e44-582">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="98e44-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="98e44-583">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="98e44-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="98e44-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="98e44-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="98e44-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="98e44-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="98e44-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | À partir d’un URI de base (par exemple, un URI précédemment retourné par <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="98e44-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="98e44-587">Le composant suivant accède au composant de l’application `Counter` lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="98e44-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="98e44-588">Le composant suivant gère un événement de modification d’emplacement en définissant <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="98e44-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="98e44-589">La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="98e44-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="98e44-590">Le déraccordement de la méthode permet de garbage collection du composant.</span><span class="sxs-lookup"><span data-stu-id="98e44-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="98e44-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fournit les informations suivantes sur l’événement :</span><span class="sxs-lookup"><span data-stu-id="98e44-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="98e44-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="98e44-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="98e44-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Si `true` , a Blazor intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="98e44-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="98e44-594">Si `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> la valeur est, la navigation a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="98e44-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="98e44-595">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="98e44-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
