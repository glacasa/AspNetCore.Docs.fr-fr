---
title: ASP.NET Core du routage éblouissant
author: guardrex
description: Découvrez comment acheminer des requêtes dans des applications et sur le composant NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/routing
ms.openlocfilehash: 197b1a91b3540d21639c3ee775b2c490da7b23fe
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030400"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="f3a92-103">ASP.NET Core du routage éblouissant</span><span class="sxs-lookup"><span data-stu-id="f3a92-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="f3a92-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f3a92-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f3a92-105">Découvrez comment acheminer les demandes et comment utiliser le `NavLink` composant pour créer des liens de navigation dans les applications éblouissantes.</span><span class="sxs-lookup"><span data-stu-id="f3a92-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="f3a92-106">Intégration du routage du point de terminaison ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3a92-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="f3a92-107">Le côté serveur éblouissant est intégré à [ASP.net Core routage des points de terminaison](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="f3a92-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="f3a92-108">Une application ASP.net Core est configurée pour accepter les connexions entrantes pour `MapBlazorHub` les `Startup.Configure`composants interactifs avec dans:</span><span class="sxs-lookup"><span data-stu-id="f3a92-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="f3a92-109">Modèles de routage</span><span class="sxs-lookup"><span data-stu-id="f3a92-109">Route templates</span></span>

<span data-ttu-id="f3a92-110">Le `Router` composant active le routage et un modèle de routage est fourni à chaque composant accessible.</span><span class="sxs-lookup"><span data-stu-id="f3a92-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="f3a92-111">Le `Router` composant apparaît dans le fichier *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="f3a92-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="f3a92-112">Dans une application côté serveur éblouissante:</span><span class="sxs-lookup"><span data-stu-id="f3a92-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="f3a92-113">Dans une application éblouissante côté client:</span><span class="sxs-lookup"><span data-stu-id="f3a92-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="f3a92-114">Lorsqu’un fichier *. Razor* avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> spécifiant le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="f3a92-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="f3a92-115">Lors de l’exécution, le routeur recherche les classes de `RouteAttribute` composant avec un et restitue le composant avec un modèle de routage qui correspond à l’URL demandée.</span><span class="sxs-lookup"><span data-stu-id="f3a92-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="f3a92-116">Plusieurs modèles de routage peuvent être appliqués à un composant.</span><span class="sxs-lookup"><span data-stu-id="f3a92-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="f3a92-117">Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="f3a92-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="f3a92-118">Pour générer correctement des itinéraires, l’application doit inclure `<base>` une balise dans son fichier *wwwroot/index.html* (le client éblouissant) ou le fichier *pages/_Host. cshtml* (le côté serveur éblouissant) avec le chemin d’accès de base de `href` l’application spécifié dans l’attribut ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="f3a92-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="f3a92-119">Pour plus d'informations, consultez <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="f3a92-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="f3a92-120">Fournir du contenu personnalisé lorsque le contenu est introuvable</span><span class="sxs-lookup"><span data-stu-id="f3a92-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="f3a92-121">Le `Router` composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.</span><span class="sxs-lookup"><span data-stu-id="f3a92-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="f3a92-122">Dans le fichier *app. Razor* , définissez le contenu personnalisé dans `<NotFoundContent>` l’élément du `Router` composant:</span><span class="sxs-lookup"><span data-stu-id="f3a92-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="f3a92-123">Le contenu de `<NotFoundContent>` peut inclure des éléments arbitraires, tels que d’autres composants interactifs.</span><span class="sxs-lookup"><span data-stu-id="f3a92-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="f3a92-124">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="f3a92-124">Route parameters</span></span>

<span data-ttu-id="f3a92-125">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse):</span><span class="sxs-lookup"><span data-stu-id="f3a92-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="f3a92-126">Les paramètres facultatifs ne sont pas pris en charge pour les applications éblouissantes dans ASP.NET Core version préliminaire 3,0.</span><span class="sxs-lookup"><span data-stu-id="f3a92-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="f3a92-127">Deux `@page` directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="f3a92-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="f3a92-128">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="f3a92-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f3a92-129">La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à `Text` la propriété.</span><span class="sxs-lookup"><span data-stu-id="f3a92-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="f3a92-130">Contraintes d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="f3a92-130">Route constraints</span></span>

<span data-ttu-id="f3a92-131">Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.</span><span class="sxs-lookup"><span data-stu-id="f3a92-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="f3a92-132">Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si:</span><span class="sxs-lookup"><span data-stu-id="f3a92-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="f3a92-133">Un `Id` segment de routage est présent sur l’URL de la demande.</span><span class="sxs-lookup"><span data-stu-id="f3a92-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="f3a92-134">Le `Id` segment est un entier (`int`).</span><span class="sxs-lookup"><span data-stu-id="f3a92-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="f3a92-135">Les contraintes de routage indiquées dans le tableau suivant sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="f3a92-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="f3a92-136">Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.</span><span class="sxs-lookup"><span data-stu-id="f3a92-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="f3a92-137">Contrainte</span><span class="sxs-lookup"><span data-stu-id="f3a92-137">Constraint</span></span> | <span data-ttu-id="f3a92-138">Exemple</span><span class="sxs-lookup"><span data-stu-id="f3a92-138">Example</span></span>           | <span data-ttu-id="f3a92-139">Exemples de correspondances</span><span class="sxs-lookup"><span data-stu-id="f3a92-139">Example Matches</span></span>                                                                  | <span data-ttu-id="f3a92-140">Invariant</span><span class="sxs-lookup"><span data-stu-id="f3a92-140">Invariant</span></span><br><span data-ttu-id="f3a92-141">culture</span><span class="sxs-lookup"><span data-stu-id="f3a92-141">culture</span></span><br><span data-ttu-id="f3a92-142">correspondance</span><span class="sxs-lookup"><span data-stu-id="f3a92-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="f3a92-143">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="f3a92-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="f3a92-144">Non</span><span class="sxs-lookup"><span data-stu-id="f3a92-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="f3a92-145">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="f3a92-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="f3a92-146">Oui</span><span class="sxs-lookup"><span data-stu-id="f3a92-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="f3a92-147">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="f3a92-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="f3a92-148">Oui</span><span class="sxs-lookup"><span data-stu-id="f3a92-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="f3a92-149">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f3a92-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f3a92-150">Oui</span><span class="sxs-lookup"><span data-stu-id="f3a92-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="f3a92-151">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="f3a92-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="f3a92-152">Oui</span><span class="sxs-lookup"><span data-stu-id="f3a92-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="f3a92-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="f3a92-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="f3a92-154">Non</span><span class="sxs-lookup"><span data-stu-id="f3a92-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="f3a92-155">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f3a92-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f3a92-156">Oui</span><span class="sxs-lookup"><span data-stu-id="f3a92-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="f3a92-157">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="f3a92-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="f3a92-158">Oui</span><span class="sxs-lookup"><span data-stu-id="f3a92-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="f3a92-159">Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant.</span><span class="sxs-lookup"><span data-stu-id="f3a92-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="f3a92-160">ces contraintes partent du principe que l’URL n’est pas localisable.</span><span class="sxs-lookup"><span data-stu-id="f3a92-160">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="f3a92-161">Composant NavLink</span><span class="sxs-lookup"><span data-stu-id="f3a92-161">NavLink component</span></span>

<span data-ttu-id="f3a92-162">Utilisez un `NavLink` composant à la place des éléments Hyperlink`<a>`html () lors de la création de liens de navigation.</span><span class="sxs-lookup"><span data-stu-id="f3a92-162">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="f3a92-163">Un `NavLink` composant se comporte comme un `<a>` élément, sauf qu’il fait basculer `active` une classe CSS selon que son `href` correspond à l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="f3a92-163">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="f3a92-164">La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.</span><span class="sxs-lookup"><span data-stu-id="f3a92-164">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="f3a92-165">Le composant `NavMenu` suivant crée une barre de navigation de [démarrage](https://getbootstrap.com/docs/) qui montre comment `NavLink` utiliser des composants:</span><span class="sxs-lookup"><span data-stu-id="f3a92-165">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="f3a92-166">Il existe deux `NavLinkMatch` options que vous pouvez assigner `Match` à l’attribut `<NavLink>` de l’élément:</span><span class="sxs-lookup"><span data-stu-id="f3a92-166">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="f3a92-167">`NavLinkMatch.All`&ndash; Estactiflorsqu’ilcorrespondàlatotalité`NavLink` de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="f3a92-167">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="f3a92-168">`NavLinkMatch.Prefix`(*par défaut*) &ndash; Estactiflorsqu’ilcorrespondàn’importequelpréfixe`NavLink` de l’URL actuelle.</span><span class="sxs-lookup"><span data-stu-id="f3a92-168">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="f3a92-169">Dans l’exemple précédent, la page `NavLink` d’hébergement `href=""` correspond à l’URL de base `active` et reçoit uniquement la classe CSS à l’URL du chemin de base par `https://localhost:5001/`défaut de l’application (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="f3a92-169">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="f3a92-170">Le deuxième `NavLink` reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple `https://localhost:5001/MyComponent` , `https://localhost:5001/MyComponent/AnotherSegment`et).</span><span class="sxs-lookup"><span data-stu-id="f3a92-170">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="f3a92-171">Les `NavLink` attributs de composant supplémentaires sont passés à la balise d’ancrage rendue.</span><span class="sxs-lookup"><span data-stu-id="f3a92-171">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="f3a92-172">Dans l’exemple suivant, le `NavLink` composant comprend l' `target` attribut:</span><span class="sxs-lookup"><span data-stu-id="f3a92-172">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="f3a92-173">Le balisage HTML suivant est rendu:</span><span class="sxs-lookup"><span data-stu-id="f3a92-173">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="f3a92-174">Aide des URI et de l’état de navigation</span><span class="sxs-lookup"><span data-stu-id="f3a92-174">URI and navigation state helpers</span></span>

<span data-ttu-id="f3a92-175">Utilisez `Microsoft.AspNetCore.Components.IUriHelper` pour travailler avec les URI et la C# navigation dans le code.</span><span class="sxs-lookup"><span data-stu-id="f3a92-175">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="f3a92-176">`IUriHelper`fournit l’événement et les méthodes répertoriées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="f3a92-176">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="f3a92-177">Membre</span><span class="sxs-lookup"><span data-stu-id="f3a92-177">Member</span></span> | <span data-ttu-id="f3a92-178">Description</span><span class="sxs-lookup"><span data-stu-id="f3a92-178">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="f3a92-179">Obtient l’URI absolu actuel.</span><span class="sxs-lookup"><span data-stu-id="f3a92-179">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="f3a92-180">Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu.</span><span class="sxs-lookup"><span data-stu-id="f3a92-180">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="f3a92-181">En général `GetBaseUri` , correspond à `href` l’attribut sur l’élément `<base>` du document dans *wwwroot/index.html* (éblouissant Client-Side) ou *pages/_Host. cshtml* (le côté serveur de éblouissant).</span><span class="sxs-lookup"><span data-stu-id="f3a92-181">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="f3a92-182">Navigue vers l’URI spécifié.</span><span class="sxs-lookup"><span data-stu-id="f3a92-182">Navigates to the specified URI.</span></span> <span data-ttu-id="f3a92-183">Si `forceLoad` est `true`:</span><span class="sxs-lookup"><span data-stu-id="f3a92-183">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="f3a92-184">Le routage côté client est contourné.</span><span class="sxs-lookup"><span data-stu-id="f3a92-184">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="f3a92-185">Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</span><span class="sxs-lookup"><span data-stu-id="f3a92-185">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="f3a92-186">Événement qui se déclenche lorsque l’emplacement de navigation a changé.</span><span class="sxs-lookup"><span data-stu-id="f3a92-186">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="f3a92-187">Convertit un URI relatif en URI absolu.</span><span class="sxs-lookup"><span data-stu-id="f3a92-187">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="f3a92-188">À partir d’un URI de base (par exemple, un URI `GetBaseUri`précédemment retourné par), convertit un URI absolu en un URI relatif au préfixe URI de base.</span><span class="sxs-lookup"><span data-stu-id="f3a92-188">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="f3a92-189">Le composant suivant accède au composant de `Counter` l’application lorsque le bouton est sélectionné:</span><span class="sxs-lookup"><span data-stu-id="f3a92-189">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```