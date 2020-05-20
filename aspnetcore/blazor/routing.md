---
<span data-ttu-id="d34b6-101">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-102">'Blazor'</span></span>
- <span data-ttu-id="d34b6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-103">'Identity'</span></span>
- <span data-ttu-id="d34b6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-105">'Razor'</span></span>
- <span data-ttu-id="d34b6-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="d34b6-107">Routage de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d34b6-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d34b6-108">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d34b6-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d34b6-109">Découvrez comment acheminer les demandes et comment utiliser le `NavLink` composant pour créer des liens de navigation dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="d34b6-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d34b6-110">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d34b6-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="d34b6-111">Le serveur est intégré à [ASP.net Core routage des points de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="d34b6-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d34b6-112">Une application ASP.NET Core est configurée pour accepter les connexions entrantes pour les composants interactifs avec `MapBlazorHub` dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d34b6-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="d34b6-113">La configuration la plus courante consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="d34b6-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="d34b6-114">Par Convention, la page *hôte* est généralement nommée *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d34b6-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="d34b6-115">L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="d34b6-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="d34b6-116">L’itinéraire de secours est pris en compte lorsque les autres itinéraires ne correspondent pas.</span><span class="sxs-lookup"><span data-stu-id="d34b6-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="d34b6-117">Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l' Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="d34b6-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="d34b6-118">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="d34b6-118">Route templates</span></span>

<span data-ttu-id="d34b6-119">Le `Router` composant active le routage vers chaque composant avec un itinéraire spécifié.</span><span class="sxs-lookup"><span data-stu-id="d34b6-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="d34b6-120">Le `Router` composant apparaît dans le fichier *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="d34b6-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="d34b6-121">Lorsqu’un fichier *. Razor* avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="d34b6-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="d34b6-122">Au moment de l’exécution, le `RouteView` composant :</span><span class="sxs-lookup"><span data-stu-id="d34b6-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="d34b6-123">Reçoit du avec les `RouteData` `Router` paramètres souhaités.</span><span class="sxs-lookup"><span data-stu-id="d34b6-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="d34b6-124">Restitue le composant spécifié avec sa disposition (ou une disposition par défaut facultative) à l’aide des paramètres spécifiés.</span><span class="sxs-lookup"><span data-stu-id="d34b6-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="d34b6-125">Vous pouvez éventuellement spécifier un `DefaultLayout` paramètre avec une classe de disposition à utiliser pour les composants qui ne spécifient pas de disposition.</span><span class="sxs-lookup"><span data-stu-id="d34b6-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="d34b6-126">Les modèles par défaut Blazor spécifient le `MainLayout` composant.</span><span class="sxs-lookup"><span data-stu-id="d34b6-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="d34b6-127">*MainLayout. Razor* se trouve dans le dossier *partagé* du projet de modèle.</span><span class="sxs-lookup"><span data-stu-id="d34b6-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="d34b6-128">Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="d34b6-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="d34b6-129">Plusieurs modèles de routage peuvent être appliqués à un composant.</span><span class="sxs-lookup"><span data-stu-id="d34b6-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d34b6-130">Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` :</span><span class="sxs-lookup"><span data-stu-id="d34b6-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="d34b6-131">Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son fichier *wwwroot/index.html* ( Blazor webassembly) ou le fichier *pages/_Host. cshtml* ( Blazor serveur) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="d34b6-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d34b6-132">Pour plus d'informations, consultez <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="d34b6-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d34b6-133">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="d34b6-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d34b6-134">Le `Router` composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="d34b6-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d34b6-135">Dans le fichier *app. Razor* , définissez le contenu personnalisé dans le `NotFound` paramètre de modèle du `Router` composant :</span><span class="sxs-lookup"><span data-stu-id="d34b6-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="d34b6-136">Le contenu des `<NotFound>` balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="d34b6-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="d34b6-137">Pour appliquer une disposition par défaut au `NotFound` contenu, consultez <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="d34b6-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="d34b6-138">Acheminer vers des composants à partir de plusieurs assemblys</span><span class="sxs-lookup"><span data-stu-id="d34b6-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="d34b6-139">Utilisez le `AdditionalAssemblies` paramètre pour spécifier des assemblys supplémentaires `Router` que le composant doit prendre en compte lors de la recherche de composants routables.</span><span class="sxs-lookup"><span data-stu-id="d34b6-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="d34b6-140">Les assemblys spécifiés sont pris en compte en plus de l' `AppAssembly` assembly spécifié.</span><span class="sxs-lookup"><span data-stu-id="d34b6-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="d34b6-141">Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de classes référencée.</span><span class="sxs-lookup"><span data-stu-id="d34b6-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="d34b6-142">L' `AdditionalAssemblies` exemple suivant entraîne la prise en charge du routage pour `Component1` :</span><span class="sxs-lookup"><span data-stu-id="d34b6-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="d34b6-143">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="d34b6-143">Route parameters</span></span>

<span data-ttu-id="d34b6-144">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse) :</span><span class="sxs-lookup"><span data-stu-id="d34b6-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="d34b6-145">Les paramètres facultatifs ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="d34b6-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="d34b6-146">Deux `@page` directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="d34b6-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="d34b6-147">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="d34b6-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d34b6-148">La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="d34b6-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="d34b6-149">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="d34b6-149">Route constraints</span></span>

<span data-ttu-id="d34b6-150">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="d34b6-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d34b6-151">Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si :</span><span class="sxs-lookup"><span data-stu-id="d34b6-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="d34b6-152">Un `Id` segment de routage est présent sur l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="d34b6-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d34b6-153">Le `Id` segment est un entier ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="d34b6-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="d34b6-154">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="d34b6-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d34b6-155">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="d34b6-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d34b6-156">Contrainte</span><span class="sxs-lookup"><span data-stu-id="d34b6-156">Constraint</span></span> | <span data-ttu-id="d34b6-157">Exemple</span><span class="sxs-lookup"><span data-stu-id="d34b6-157">Example</span></span>           | <span data-ttu-id="d34b6-158">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="d34b6-158">Example Matches</span></span>                                                                  | <span data-ttu-id="d34b6-159">Invariant</span><span class="sxs-lookup"><span data-stu-id="d34b6-159">Invariant</span></span><br><span data-ttu-id="d34b6-160">culture</span><span class="sxs-lookup"><span data-stu-id="d34b6-160">culture</span></span><br><span data-ttu-id="d34b6-161">correspondance</span><span class="sxs-lookup"><span data-stu-id="d34b6-161">matching</span></span> |
| ---
<span data-ttu-id="d34b6-162">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-163">'Blazor'</span></span>
- <span data-ttu-id="d34b6-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-164">'Identity'</span></span>
- <span data-ttu-id="d34b6-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-166">'Razor'</span></span>
- <span data-ttu-id="d34b6-167">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-168">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-169">'Blazor'</span></span>
- <span data-ttu-id="d34b6-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-170">'Identity'</span></span>
- <span data-ttu-id="d34b6-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-172">'Razor'</span></span>
- <span data-ttu-id="d34b6-173">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-174">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-175">'Blazor'</span></span>
- <span data-ttu-id="d34b6-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-176">'Identity'</span></span>
- <span data-ttu-id="d34b6-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-178">'Razor'</span></span>
- <span data-ttu-id="d34b6-179">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-179">'SignalR' uid:</span></span> 

<span data-ttu-id="d34b6-180">----- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-181">'Blazor'</span></span>
- <span data-ttu-id="d34b6-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-182">'Identity'</span></span>
- <span data-ttu-id="d34b6-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-184">'Razor'</span></span>
- <span data-ttu-id="d34b6-185">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-186">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-187">'Blazor'</span></span>
- <span data-ttu-id="d34b6-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-188">'Identity'</span></span>
- <span data-ttu-id="d34b6-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-190">'Razor'</span></span>
- <span data-ttu-id="d34b6-191">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-192">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-193">'Blazor'</span></span>
- <span data-ttu-id="d34b6-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-194">'Identity'</span></span>
- <span data-ttu-id="d34b6-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-196">'Razor'</span></span>
- <span data-ttu-id="d34b6-197">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-198">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-199">'Blazor'</span></span>
- <span data-ttu-id="d34b6-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-200">'Identity'</span></span>
- <span data-ttu-id="d34b6-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-202">'Razor'</span></span>
- <span data-ttu-id="d34b6-203">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-204">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-205">'Blazor'</span></span>
- <span data-ttu-id="d34b6-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-206">'Identity'</span></span>
- <span data-ttu-id="d34b6-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-208">'Razor'</span></span>
- <span data-ttu-id="d34b6-209">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-210">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-211">'Blazor'</span></span>
- <span data-ttu-id="d34b6-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-212">'Identity'</span></span>
- <span data-ttu-id="d34b6-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-214">'Razor'</span></span>
- <span data-ttu-id="d34b6-215">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-215">'SignalR' uid:</span></span> 

<span data-ttu-id="d34b6-216">--------- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-217">'Blazor'</span></span>
- <span data-ttu-id="d34b6-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-218">'Identity'</span></span>
- <span data-ttu-id="d34b6-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-220">'Razor'</span></span>
- <span data-ttu-id="d34b6-221">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-222">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-223">'Blazor'</span></span>
- <span data-ttu-id="d34b6-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-224">'Identity'</span></span>
- <span data-ttu-id="d34b6-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-226">'Razor'</span></span>
- <span data-ttu-id="d34b6-227">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-228">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-229">'Blazor'</span></span>
- <span data-ttu-id="d34b6-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-230">'Identity'</span></span>
- <span data-ttu-id="d34b6-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-232">'Razor'</span></span>
- <span data-ttu-id="d34b6-233">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-234">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-235">'Blazor'</span></span>
- <span data-ttu-id="d34b6-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-236">'Identity'</span></span>
- <span data-ttu-id="d34b6-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-238">'Razor'</span></span>
- <span data-ttu-id="d34b6-239">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-240">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-241">'Blazor'</span></span>
- <span data-ttu-id="d34b6-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-242">'Identity'</span></span>
- <span data-ttu-id="d34b6-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-244">'Razor'</span></span>
- <span data-ttu-id="d34b6-245">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-246">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-247">'Blazor'</span></span>
- <span data-ttu-id="d34b6-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-248">'Identity'</span></span>
- <span data-ttu-id="d34b6-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-250">'Razor'</span></span>
- <span data-ttu-id="d34b6-251">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-252">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-253">'Blazor'</span></span>
- <span data-ttu-id="d34b6-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-254">'Identity'</span></span>
- <span data-ttu-id="d34b6-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-256">'Razor'</span></span>
- <span data-ttu-id="d34b6-257">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-258">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-259">'Blazor'</span></span>
- <span data-ttu-id="d34b6-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-260">'Identity'</span></span>
- <span data-ttu-id="d34b6-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-262">'Razor'</span></span>
- <span data-ttu-id="d34b6-263">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-264">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-265">'Blazor'</span></span>
- <span data-ttu-id="d34b6-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-266">'Identity'</span></span>
- <span data-ttu-id="d34b6-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-268">'Razor'</span></span>
- <span data-ttu-id="d34b6-269">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-270">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-271">'Blazor'</span></span>
- <span data-ttu-id="d34b6-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-272">'Identity'</span></span>
- <span data-ttu-id="d34b6-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-274">'Razor'</span></span>
- <span data-ttu-id="d34b6-275">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-276">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-277">'Blazor'</span></span>
- <span data-ttu-id="d34b6-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-278">'Identity'</span></span>
- <span data-ttu-id="d34b6-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-280">'Razor'</span></span>
- <span data-ttu-id="d34b6-281">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-282">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-283">'Blazor'</span></span>
- <span data-ttu-id="d34b6-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-284">'Identity'</span></span>
- <span data-ttu-id="d34b6-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-286">'Razor'</span></span>
- <span data-ttu-id="d34b6-287">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-288">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-289">'Blazor'</span></span>
- <span data-ttu-id="d34b6-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-290">'Identity'</span></span>
- <span data-ttu-id="d34b6-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-292">'Razor'</span></span>
- <span data-ttu-id="d34b6-293">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-294">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-295">'Blazor'</span></span>
- <span data-ttu-id="d34b6-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-296">'Identity'</span></span>
- <span data-ttu-id="d34b6-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-298">'Razor'</span></span>
- <span data-ttu-id="d34b6-299">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-300">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-301">'Blazor'</span></span>
- <span data-ttu-id="d34b6-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-302">'Identity'</span></span>
- <span data-ttu-id="d34b6-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-304">'Razor'</span></span>
- <span data-ttu-id="d34b6-305">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-306">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-307">'Blazor'</span></span>
- <span data-ttu-id="d34b6-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-308">'Identity'</span></span>
- <span data-ttu-id="d34b6-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-310">'Razor'</span></span>
- <span data-ttu-id="d34b6-311">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-312">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-313">'Blazor'</span></span>
- <span data-ttu-id="d34b6-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-314">'Identity'</span></span>
- <span data-ttu-id="d34b6-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-316">'Razor'</span></span>
- <span data-ttu-id="d34b6-317">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-318">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-319">'Blazor'</span></span>
- <span data-ttu-id="d34b6-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-320">'Identity'</span></span>
- <span data-ttu-id="d34b6-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-322">'Razor'</span></span>
- <span data-ttu-id="d34b6-323">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-324">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-325">'Blazor'</span></span>
- <span data-ttu-id="d34b6-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-326">'Identity'</span></span>
- <span data-ttu-id="d34b6-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-328">'Razor'</span></span>
- <span data-ttu-id="d34b6-329">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-330">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-331">'Blazor'</span></span>
- <span data-ttu-id="d34b6-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-332">'Identity'</span></span>
- <span data-ttu-id="d34b6-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-334">'Razor'</span></span>
- <span data-ttu-id="d34b6-335">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-336">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-337">'Blazor'</span></span>
- <span data-ttu-id="d34b6-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-338">'Identity'</span></span>
- <span data-ttu-id="d34b6-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-340">'Razor'</span></span>
- <span data-ttu-id="d34b6-341">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-342">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-343">'Blazor'</span></span>
- <span data-ttu-id="d34b6-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-344">'Identity'</span></span>
- <span data-ttu-id="d34b6-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-346">'Razor'</span></span>
- <span data-ttu-id="d34b6-347">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-348">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-349">'Blazor'</span></span>
- <span data-ttu-id="d34b6-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-350">'Identity'</span></span>
- <span data-ttu-id="d34b6-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-352">'Razor'</span></span>
- <span data-ttu-id="d34b6-353">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-354">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-355">'Blazor'</span></span>
- <span data-ttu-id="d34b6-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-356">'Identity'</span></span>
- <span data-ttu-id="d34b6-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-358">'Razor'</span></span>
- <span data-ttu-id="d34b6-359">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-360">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-361">'Blazor'</span></span>
- <span data-ttu-id="d34b6-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-362">'Identity'</span></span>
- <span data-ttu-id="d34b6-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-364">'Razor'</span></span>
- <span data-ttu-id="d34b6-365">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-366">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-367">'Blazor'</span></span>
- <span data-ttu-id="d34b6-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-368">'Identity'</span></span>
- <span data-ttu-id="d34b6-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-370">'Razor'</span></span>
- <span data-ttu-id="d34b6-371">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-372">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-373">'Blazor'</span></span>
- <span data-ttu-id="d34b6-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-374">'Identity'</span></span>
- <span data-ttu-id="d34b6-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-376">'Razor'</span></span>
- <span data-ttu-id="d34b6-377">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-378">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-379">'Blazor'</span></span>
- <span data-ttu-id="d34b6-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-380">'Identity'</span></span>
- <span data-ttu-id="d34b6-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-382">'Razor'</span></span>
- <span data-ttu-id="d34b6-383">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-384">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-385">'Blazor'</span></span>
- <span data-ttu-id="d34b6-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-386">'Identity'</span></span>
- <span data-ttu-id="d34b6-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-388">'Razor'</span></span>
- <span data-ttu-id="d34b6-389">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-390">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-391">'Blazor'</span></span>
- <span data-ttu-id="d34b6-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-392">'Identity'</span></span>
- <span data-ttu-id="d34b6-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-394">'Razor'</span></span>
- <span data-ttu-id="d34b6-395">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-396">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-397">'Blazor'</span></span>
- <span data-ttu-id="d34b6-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-398">'Identity'</span></span>
- <span data-ttu-id="d34b6-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-400">'Razor'</span></span>
- <span data-ttu-id="d34b6-401">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-402">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-403">'Blazor'</span></span>
- <span data-ttu-id="d34b6-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-404">'Identity'</span></span>
- <span data-ttu-id="d34b6-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-406">'Razor'</span></span>
- <span data-ttu-id="d34b6-407">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-408">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-409">'Blazor'</span></span>
- <span data-ttu-id="d34b6-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-410">'Identity'</span></span>
- <span data-ttu-id="d34b6-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-412">'Razor'</span></span>
- <span data-ttu-id="d34b6-413">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-414">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-415">'Blazor'</span></span>
- <span data-ttu-id="d34b6-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-416">'Identity'</span></span>
- <span data-ttu-id="d34b6-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-418">'Razor'</span></span>
- <span data-ttu-id="d34b6-419">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-420">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-421">'Blazor'</span></span>
- <span data-ttu-id="d34b6-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-422">'Identity'</span></span>
- <span data-ttu-id="d34b6-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-424">'Razor'</span></span>
- <span data-ttu-id="d34b6-425">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-426">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-427">'Blazor'</span></span>
- <span data-ttu-id="d34b6-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-428">'Identity'</span></span>
- <span data-ttu-id="d34b6-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-430">'Razor'</span></span>
- <span data-ttu-id="d34b6-431">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-432">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-433">'Blazor'</span></span>
- <span data-ttu-id="d34b6-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-434">'Identity'</span></span>
- <span data-ttu-id="d34b6-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-436">'Razor'</span></span>
- <span data-ttu-id="d34b6-437">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-438">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-439">'Blazor'</span></span>
- <span data-ttu-id="d34b6-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-440">'Identity'</span></span>
- <span data-ttu-id="d34b6-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-442">'Razor'</span></span>
- <span data-ttu-id="d34b6-443">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-443">'SignalR' uid:</span></span> 

<span data-ttu-id="d34b6-444">---------------------------------------- | :---titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-445">'Blazor'</span></span>
- <span data-ttu-id="d34b6-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-446">'Identity'</span></span>
- <span data-ttu-id="d34b6-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-448">'Razor'</span></span>
- <span data-ttu-id="d34b6-449">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-450">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-451">'Blazor'</span></span>
- <span data-ttu-id="d34b6-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-452">'Identity'</span></span>
- <span data-ttu-id="d34b6-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-454">'Razor'</span></span>
- <span data-ttu-id="d34b6-455">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-456">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-457">'Blazor'</span></span>
- <span data-ttu-id="d34b6-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-458">'Identity'</span></span>
- <span data-ttu-id="d34b6-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-460">'Razor'</span></span>
- <span data-ttu-id="d34b6-461">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-462">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-463">'Blazor'</span></span>
- <span data-ttu-id="d34b6-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-464">'Identity'</span></span>
- <span data-ttu-id="d34b6-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-466">'Razor'</span></span>
- <span data-ttu-id="d34b6-467">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-468">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-469">'Blazor'</span></span>
- <span data-ttu-id="d34b6-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-470">'Identity'</span></span>
- <span data-ttu-id="d34b6-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-472">'Razor'</span></span>
- <span data-ttu-id="d34b6-473">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-474">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-475">'Blazor'</span></span>
- <span data-ttu-id="d34b6-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-476">'Identity'</span></span>
- <span data-ttu-id="d34b6-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-478">'Razor'</span></span>
- <span data-ttu-id="d34b6-479">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-480">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-481">'Blazor'</span></span>
- <span data-ttu-id="d34b6-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-482">'Identity'</span></span>
- <span data-ttu-id="d34b6-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-484">'Razor'</span></span>
- <span data-ttu-id="d34b6-485">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-486">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-487">'Blazor'</span></span>
- <span data-ttu-id="d34b6-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-488">'Identity'</span></span>
- <span data-ttu-id="d34b6-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-490">'Razor'</span></span>
- <span data-ttu-id="d34b6-491">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-492">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-493">'Blazor'</span></span>
- <span data-ttu-id="d34b6-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-494">'Identity'</span></span>
- <span data-ttu-id="d34b6-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-496">'Razor'</span></span>
- <span data-ttu-id="d34b6-497">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-498">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-499">'Blazor'</span></span>
- <span data-ttu-id="d34b6-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-500">'Identity'</span></span>
- <span data-ttu-id="d34b6-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-502">'Razor'</span></span>
- <span data-ttu-id="d34b6-503">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-504">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-505">'Blazor'</span></span>
- <span data-ttu-id="d34b6-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-506">'Identity'</span></span>
- <span data-ttu-id="d34b6-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-508">'Razor'</span></span>
- <span data-ttu-id="d34b6-509">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-510">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-511">'Blazor'</span></span>
- <span data-ttu-id="d34b6-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-512">'Identity'</span></span>
- <span data-ttu-id="d34b6-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-514">'Razor'</span></span>
- <span data-ttu-id="d34b6-515">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-516">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-517">'Blazor'</span></span>
- <span data-ttu-id="d34b6-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-518">'Identity'</span></span>
- <span data-ttu-id="d34b6-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-520">'Razor'</span></span>
- <span data-ttu-id="d34b6-521">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-521">'SignalR' uid:</span></span> 

<span data-ttu-id="d34b6-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Non | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Oui | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Oui | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Oui | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Oui | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Non | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Oui | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Oui |</span><span class="sxs-lookup"><span data-stu-id="d34b6-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="d34b6-523">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="d34b6-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="d34b6-524">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="d34b6-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="d34b6-525">Routage avec des URL qui contiennent des points</span><span class="sxs-lookup"><span data-stu-id="d34b6-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="d34b6-526">Dans Blazor les applications serveur, l’itinéraire par défaut dans *_Host. cshtml* est `/` ( `@page "/"` ).</span><span class="sxs-lookup"><span data-stu-id="d34b6-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="d34b6-527">Une URL de demande qui contient un point ( `.` ) ne correspond pas à l’itinéraire par défaut, car l’URL semble demander un fichier.</span><span class="sxs-lookup"><span data-stu-id="d34b6-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="d34b6-528">Une Blazor application renvoie une réponse *404-introuvable* pour un fichier statique qui n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="d34b6-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="d34b6-529">Pour utiliser des itinéraires qui contiennent un point, configurez *_Host. cshtml* avec le modèle d’itinéraire suivant :</span><span class="sxs-lookup"><span data-stu-id="d34b6-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="d34b6-530">Le `"/{**path}"` modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="d34b6-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="d34b6-531">Double-astérisque syntaxe *catch-all* ( `**` ) pour capturer le chemin d’accès dans plusieurs limites de dossiers sans encodage des barres obliques ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="d34b6-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="d34b6-532">`path`nom du paramètre d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="d34b6-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="d34b6-533">La syntaxe de paramètre *catch-all* ( `*` / `**` ) n’est **pas** prise en charge dans les Razor composants (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="d34b6-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="d34b6-534">Pour plus d'informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d34b6-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="d34b6-535">Composant NavLink</span><span class="sxs-lookup"><span data-stu-id="d34b6-535">NavLink component</span></span>

<span data-ttu-id="d34b6-536">Utilisez un `NavLink` composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="d34b6-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d34b6-537">Un `NavLink` composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="d34b6-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d34b6-538">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="d34b6-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="d34b6-539">Le `NavMenu` composant suivant crée une barre de navigation de [démarrage](https://getbootstrap.com/docs/) qui montre comment utiliser des `NavLink` composants :</span><span class="sxs-lookup"><span data-stu-id="d34b6-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="d34b6-540">Il existe deux `NavLinkMatch` options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :</span><span class="sxs-lookup"><span data-stu-id="d34b6-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d34b6-541">`NavLinkMatch.All`&ndash; `NavLink` Est actif lorsqu’il correspond à la totalité de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="d34b6-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d34b6-542">`NavLinkMatch.Prefix`(*par défaut*) &ndash; `NavLink`Est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="d34b6-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d34b6-543">Dans l’exemple précédent, la page `NavLink` `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="d34b6-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d34b6-544">Le deuxième `NavLink` reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="d34b6-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="d34b6-545">`NavLink`Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="d34b6-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="d34b6-546">Dans l’exemple suivant, le `NavLink` composant comprend l' `target` attribut :</span><span class="sxs-lookup"><span data-stu-id="d34b6-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="d34b6-547">Le balisage HTML suivant est rendu :</span><span class="sxs-lookup"><span data-stu-id="d34b6-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d34b6-548">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="d34b6-548">URI and navigation state helpers</span></span>

<span data-ttu-id="d34b6-549">Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les URI et la navigation dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="d34b6-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="d34b6-550">`NavigationManager`fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="d34b6-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d34b6-551">Membre</span><span class="sxs-lookup"><span data-stu-id="d34b6-551">Member</span></span> | <span data-ttu-id="d34b6-552">Description</span><span class="sxs-lookup"><span data-stu-id="d34b6-552">Description</span></span> |
| ---
<span data-ttu-id="d34b6-553">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-554">'Blazor'</span></span>
- <span data-ttu-id="d34b6-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-555">'Identity'</span></span>
- <span data-ttu-id="d34b6-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-557">'Razor'</span></span>
- <span data-ttu-id="d34b6-558">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-558">'SignalR' uid:</span></span> 

<span data-ttu-id="d34b6-559">--- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-560">'Blazor'</span></span>
- <span data-ttu-id="d34b6-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-561">'Identity'</span></span>
- <span data-ttu-id="d34b6-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-563">'Razor'</span></span>
- <span data-ttu-id="d34b6-564">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-565">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-566">'Blazor'</span></span>
- <span data-ttu-id="d34b6-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-567">'Identity'</span></span>
- <span data-ttu-id="d34b6-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-569">'Razor'</span></span>
- <span data-ttu-id="d34b6-570">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d34b6-571">titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="d34b6-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d34b6-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-572">'Blazor'</span></span>
- <span data-ttu-id="d34b6-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d34b6-573">'Identity'</span></span>
- <span data-ttu-id="d34b6-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d34b6-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="d34b6-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d34b6-575">'Razor'</span></span>
- <span data-ttu-id="d34b6-576">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="d34b6-576">'SignalR' uid:</span></span> 

<span data-ttu-id="d34b6-577">------ | | URI | Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="d34b6-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="d34b6-578">| | BaseUri | Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="d34b6-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d34b6-579">En général, `BaseUri` correspond à l' `href` attribut sur l’élément du document `<base>` dans *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="d34b6-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="d34b6-580">| | NavigateTo | Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="d34b6-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="d34b6-581">Si `forceLoad` est `true` :</span><span class="sxs-lookup"><span data-stu-id="d34b6-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d34b6-582">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="d34b6-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d34b6-583">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="d34b6-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="d34b6-584">| | LocationChanged | Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="d34b6-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="d34b6-585">| | ToAbsoluteUri | Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="d34b6-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="d34b6-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | À partir d’un URI de base (par exemple, un URI précédemment retourné par `GetBaseUri` ), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="d34b6-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d34b6-587">Le composant suivant accède au composant de l’application `Counter` lorsque le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="d34b6-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="d34b6-588">Le composant suivant gère un événement de modification d’emplacement.</span><span class="sxs-lookup"><span data-stu-id="d34b6-588">The following component handles a location changed event.</span></span> <span data-ttu-id="d34b6-589">La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="d34b6-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="d34b6-590">Le déraccordement de la méthode permet de garbage collection du composant.</span><span class="sxs-lookup"><span data-stu-id="d34b6-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="d34b6-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fournit les informations suivantes sur l’événement :</span><span class="sxs-lookup"><span data-stu-id="d34b6-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="d34b6-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;URL du nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="d34b6-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="d34b6-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Si `true` , a Blazor intercepté la navigation à partir du navigateur.</span><span class="sxs-lookup"><span data-stu-id="d34b6-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="d34b6-594">Si `false` la valeur est, [NavigationManager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) a entraîné la navigation.</span><span class="sxs-lookup"><span data-stu-id="d34b6-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="d34b6-595">Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="d34b6-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
