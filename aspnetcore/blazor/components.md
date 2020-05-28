---
<span data-ttu-id="ee834-101">titre : « créer et utiliser des Razor composants ASP.net Core » auteur : Description : « Découvrez comment créer et utiliser des Razor composants, y compris comment lier des données, gérer des événements et gérer des cycles de vie de composant. »</span><span class="sxs-lookup"><span data-stu-id="ee834-101">title: 'Create and use ASP.NET Core Razor components' author: description: 'Learn how to create and use Razor components, including how to bind to data, handle events, and manage component life cycles.'</span></span>
<span data-ttu-id="ee834-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="ee834-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ee834-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ee834-103">'Blazor'</span></span>
- <span data-ttu-id="ee834-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ee834-104">'Identity'</span></span>
- <span data-ttu-id="ee834-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ee834-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ee834-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ee834-106">'Razor'</span></span>
- <span data-ttu-id="ee834-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="ee834-107">'SignalR' uid:</span></span> 

---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="ee834-108">Créer et utiliser des Razor composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ee834-108">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="ee834-109">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="ee834-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="ee834-110">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee834-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="ee834-111">les applications sont générées à l’aide de *composants*.</span><span class="sxs-lookup"><span data-stu-id="ee834-111"> apps are built using *components*.</span></span> <span data-ttu-id="ee834-112">Un composant est un bloc d’interface utilisateur (IU) autonome, tel qu’une page, une boîte de dialogue ou un formulaire.</span><span class="sxs-lookup"><span data-stu-id="ee834-112">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="ee834-113">Un composant comprend le balisage HTML et la logique de traitement requise pour injecter des données ou répondre à des événements d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ee834-113">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="ee834-114">Les composants sont flexibles et légers.</span><span class="sxs-lookup"><span data-stu-id="ee834-114">Components are flexible and lightweight.</span></span> <span data-ttu-id="ee834-115">Elles peuvent être imbriquées, réutilisées et partagées entre les projets.</span><span class="sxs-lookup"><span data-stu-id="ee834-115">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="ee834-116">Classes de composant</span><span class="sxs-lookup"><span data-stu-id="ee834-116">Component classes</span></span>

<span data-ttu-id="ee834-117">Les composants sont implémentés dans les [Razor](xref:mvc/views/razor) fichiers de composants (*. Razor*) à l’aide d’une combinaison de balisage C# et html.</span><span class="sxs-lookup"><span data-stu-id="ee834-117">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="ee834-118">Un composant dans Blazor est officiellement désigné sous le terme de \* Razor composant\*.</span><span class="sxs-lookup"><span data-stu-id="ee834-118">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="ee834-119">Le nom d’un composant doit commencer par un caractère majuscule.</span><span class="sxs-lookup"><span data-stu-id="ee834-119">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="ee834-120">Par exemple, *MyCoolComponent. Razor* est valide et *MyCoolComponent. Razor* n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="ee834-120">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="ee834-121">L’interface utilisateur d’un composant est définie à l’aide de HTML.</span><span class="sxs-lookup"><span data-stu-id="ee834-121">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="ee834-122">La logique de rendu dynamique (par exemple, les boucles, les instructions conditionnelles, les expressions) est ajoutée à l’aide d’une syntaxe C# incorporée appelée *Razor* .</span><span class="sxs-lookup"><span data-stu-id="ee834-122">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="ee834-123">Quand une application est compilée, le balisage HTML et la logique de rendu C# sont convertis en une classe de composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-123">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="ee834-124">Le nom de la classe générée correspond au nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="ee834-124">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="ee834-125">Les membres de la classe Component sont définis dans un [`@code`][1] bloc.</span><span class="sxs-lookup"><span data-stu-id="ee834-125">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="ee834-126">Dans le [`@code`][1] bloc, l’état du composant (propriétés, champs) est spécifié avec des méthodes pour la gestion des événements ou pour la définition d’une autre logique de composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-126">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="ee834-127">Plus d’un [`@code`][1] bloc est autorisé.</span><span class="sxs-lookup"><span data-stu-id="ee834-127">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="ee834-128">Les membres de composant peuvent être utilisés dans le cadre de la logique de rendu du composant à l’aide d’expressions C# qui commencent par `@` .</span><span class="sxs-lookup"><span data-stu-id="ee834-128">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="ee834-129">Par exemple, un champ C# est rendu en préfixant `@` le nom du champ.</span><span class="sxs-lookup"><span data-stu-id="ee834-129">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="ee834-130">L’exemple suivant évalue et affiche :</span><span class="sxs-lookup"><span data-stu-id="ee834-130">The following example evaluates and renders:</span></span>

* <span data-ttu-id="ee834-131">`headingFontStyle`à la valeur de propriété CSS pour `font-style` .</span><span class="sxs-lookup"><span data-stu-id="ee834-131">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="ee834-132">`headingText`au contenu de l' `<h1>` élément.</span><span class="sxs-lookup"><span data-stu-id="ee834-132">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="ee834-133">Une fois le composant restitué initialement, le composant régénère son arborescence de rendu en réponse aux événements.</span><span class="sxs-lookup"><span data-stu-id="ee834-133">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="ee834-134">compare ensuite la nouvelle arborescence de rendu à la précédente et applique toutes les modifications apportées au Document Object Model du navigateur (DOM).</span><span class="sxs-lookup"><span data-stu-id="ee834-134"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="ee834-135">Les composants sont des classes C# ordinaires qui peuvent être placées n’importe où dans un projet.</span><span class="sxs-lookup"><span data-stu-id="ee834-135">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="ee834-136">Les composants qui produisent des pages Web résident généralement dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="ee834-136">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="ee834-137">Les composants qui ne sont pas des pages sont souvent placés dans le dossier *partagé* ou dans un dossier personnalisé ajouté au projet.</span><span class="sxs-lookup"><span data-stu-id="ee834-137">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="ee834-138">En règle générale, l’espace de noms d’un composant est dérivé de l’espace de noms racine de l’application et de l’emplacement (dossier) du composant dans l’application.</span><span class="sxs-lookup"><span data-stu-id="ee834-138">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="ee834-139">Si l’espace de noms racine de l’application est `BlazorApp` et que le `Counter` composant se trouve dans le dossier *pages* :</span><span class="sxs-lookup"><span data-stu-id="ee834-139">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="ee834-140">L' `Counter` espace de noms du composant est `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="ee834-140">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="ee834-141">Le nom de type qualifié complet du composant est `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="ee834-141">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="ee834-142">Pour les dossiers personnalisés qui contiennent des composants, ajoutez une `using` instruction au composant parent ou au fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="ee834-142">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="ee834-143">L’exemple suivant rend les composants du dossier *composants* disponibles :</span><span class="sxs-lookup"><span data-stu-id="ee834-143">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="ee834-144">Un composant peut également être référencé directement :</span><span class="sxs-lookup"><span data-stu-id="ee834-144">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="ee834-145">Pour plus d’informations, consultez la section [importer des composants](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="ee834-145">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="ee834-146">Syntaxe de Razor</span><span class="sxs-lookup"><span data-stu-id="ee834-146">Razor syntax</span></span>

Razor<span data-ttu-id="ee834-147">les composants dans les Blazor applications utilisent largement la Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="ee834-147"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="ee834-148">Si vous n’êtes pas familiarisé avec le Razor langage de balisage, nous vous recommandons de lire <xref:mvc/views/razor> avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="ee834-148">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="ee834-149">Lorsque vous accédez au contenu sur la Razor syntaxe, portez une attention particulière aux sections suivantes :</span><span class="sxs-lookup"><span data-stu-id="ee834-149">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="ee834-150">[Directives](xref:mvc/views/razor#directives): `@` -préfixe des mots clés réservés qui modifient généralement la manière dont le balisage du composant est analysé ou fonction.</span><span class="sxs-lookup"><span data-stu-id="ee834-150">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="ee834-151">[Attributs de directive](xref:mvc/views/razor#directive-attributes): `@` Mots clés réservés préfixés qui modifient généralement le mode d’analyse ou de fonction des éléments de composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-151">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

## <a name="static-assets"></a><span data-ttu-id="ee834-152">Les ressources statiques</span><span class="sxs-lookup"><span data-stu-id="ee834-152">Static assets</span></span>

Blazor<span data-ttu-id="ee834-153">suit la Convention de ASP.NET Core les applications qui placent des ressources statiques dans le [dossier racine Web (Wwwroot)](xref:fundamentals/index#web-root)du projet.</span><span class="sxs-lookup"><span data-stu-id="ee834-153"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="ee834-154">Utilisez un chemin d’accès relatif à `/` la base () pour faire référence à la racine Web d’une ressource statique.</span><span class="sxs-lookup"><span data-stu-id="ee834-154">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="ee834-155">Dans l’exemple suivant, *logo. png* se trouve physiquement dans le dossier *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="ee834-155">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="ee834-156">les composants ne prennent **pas** en charge la notation tilde-slash ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="ee834-156"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="ee834-157">Pour plus d’informations sur la définition du chemin d’accès de base d’une application, consultez <xref:host-and-deploy/blazor/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="ee834-157">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="ee834-158">Les balises tag Helper ne sont pas prises en charge dans les composants</span><span class="sxs-lookup"><span data-stu-id="ee834-158">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="ee834-159">Les [tag helpers](xref:mvc/views/tag-helpers/intro) ne sont pas pris en charge dans les Razor composants (fichiers *. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="ee834-159">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="ee834-160">Pour fournir des fonctionnalités semblables à tag Helper dans Blazor , créez un composant avec les mêmes fonctionnalités que le tag Helper et utilisez le composant à la place.</span><span class="sxs-lookup"><span data-stu-id="ee834-160">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="ee834-161">Utiliser des composants</span><span class="sxs-lookup"><span data-stu-id="ee834-161">Use components</span></span>

<span data-ttu-id="ee834-162">Les composants peuvent inclure d’autres composants en les déclarant à l’aide de la syntaxe d’élément HTML.</span><span class="sxs-lookup"><span data-stu-id="ee834-162">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="ee834-163">Le balisage pour l’utilisation d’un composant ressemble à une balise HTML où le nom de la balise est le type du composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-163">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="ee834-164">Le balisage suivant dans *index. Razor* rend une `HeadingComponent` instance de :</span><span class="sxs-lookup"><span data-stu-id="ee834-164">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="ee834-165">*Composants/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-165">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="ee834-166">Si un composant contient un élément HTML avec une première lettre majuscule qui ne correspond pas à un nom de composant, un avertissement est émis pour indiquer que l’élément a un nom inattendu.</span><span class="sxs-lookup"><span data-stu-id="ee834-166">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="ee834-167">L’ajout d’une [`@using`][2] directive pour l’espace de noms du composant rend le composant disponible, ce qui résout l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="ee834-167">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="ee834-168">Routage</span><span class="sxs-lookup"><span data-stu-id="ee834-168">Routing</span></span>

<span data-ttu-id="ee834-169">Le routage dans Blazor est effectué en fournissant un modèle de routage à chaque composant accessible dans l’application.</span><span class="sxs-lookup"><span data-stu-id="ee834-169">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="ee834-170">Lorsqu’un Razor fichier avec une [`@page`][9] directive est compilé, la classe générée reçoit un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> qui spécifie le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="ee834-170">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="ee834-171">Lors de l’exécution, le routeur recherche les classes de composant avec un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> et rend le composant qui a un modèle de routage correspondant à l’URL demandée.</span><span class="sxs-lookup"><span data-stu-id="ee834-171">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="ee834-172">Pour plus d'informations, consultez <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="ee834-172">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="ee834-173">Paramètres</span><span class="sxs-lookup"><span data-stu-id="ee834-173">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="ee834-174">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="ee834-174">Route parameters</span></span>

<span data-ttu-id="ee834-175">Les composants peuvent recevoir des paramètres de routage du modèle de routage fourni dans la [`@page`][9] directive.</span><span class="sxs-lookup"><span data-stu-id="ee834-175">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="ee834-176">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants.</span><span class="sxs-lookup"><span data-stu-id="ee834-176">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="ee834-177">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-177">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="ee834-178">Les paramètres facultatifs ne sont pas pris en charge [`@page`][9] . deux directives sont donc appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="ee834-178">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="ee834-179">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="ee834-179">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ee834-180">La deuxième [`@page`][9] directive reçoit le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="ee834-180">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="ee834-181">La syntaxe de paramètre *catch-all* ( `*` / `**` ), qui capture le chemin d’accès dans plusieurs limites de dossiers, n’est **pas** prise en charge dans les Razor composants (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ee834-181">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="ee834-182">Paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="ee834-182">Component parameters</span></span>

<span data-ttu-id="ee834-183">Les composants peuvent avoir des *paramètres de composant*, qui sont définis à l’aide de propriétés publiques sur la classe de composant avec l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="ee834-183">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="ee834-184">Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.</span><span class="sxs-lookup"><span data-stu-id="ee834-184">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="ee834-185">*Composants/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="ee834-186">Dans l’exemple suivant tiré de l’exemple d’application, le `ParentComponent` définit la valeur de la `Title` propriété de `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="ee834-186">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="ee834-187">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="ee834-188">Ne créez pas de composants qui écrivent dans leurs propres *paramètres de composant*, utilisez un champ privé à la place.</span><span class="sxs-lookup"><span data-stu-id="ee834-188">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="ee834-189">Pour plus d’informations, consultez la section [ne pas créer de composants qui écrivent dans leur propre propriété de paramètre](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="ee834-189">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="ee834-190">Contenu enfant</span><span class="sxs-lookup"><span data-stu-id="ee834-190">Child content</span></span>

<span data-ttu-id="ee834-191">Les composants peuvent définir le contenu d’un autre composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-191">Components can set the content of another component.</span></span> <span data-ttu-id="ee834-192">Le composant d’affectation fournit le contenu entre les balises qui spécifient le composant récepteur.</span><span class="sxs-lookup"><span data-stu-id="ee834-192">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="ee834-193">Dans l’exemple suivant, `ChildComponent` a une `ChildContent` propriété qui représente un <xref:Microsoft.AspNetCore.Components.RenderFragment> , qui représente un segment de l’interface utilisateur à restituer.</span><span class="sxs-lookup"><span data-stu-id="ee834-193">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="ee834-194">La valeur de `ChildContent` est positionnée dans le balisage du composant où le contenu doit être rendu.</span><span class="sxs-lookup"><span data-stu-id="ee834-194">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="ee834-195">La valeur de `ChildContent` est reçue du composant parent et rendue à l’intérieur du panneau de démarrage `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="ee834-195">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="ee834-196">*Composants/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-196">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="ee834-197">La propriété qui reçoit le <xref:Microsoft.AspNetCore.Components.RenderFragment> contenu doit être nommée `ChildContent` par Convention.</span><span class="sxs-lookup"><span data-stu-id="ee834-197">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="ee834-198">`ParentComponent`Dans l’exemple d’application, vous pouvez fournir du contenu pour le rendu de `ChildComponent` en plaçant le contenu à l’intérieur des `<ChildComponent>` balises.</span><span class="sxs-lookup"><span data-stu-id="ee834-198">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="ee834-199">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-199">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="ee834-200">Réprojection d’attribut et paramètres arbitraires</span><span class="sxs-lookup"><span data-stu-id="ee834-200">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="ee834-201">Les composants peuvent capturer et restituer des attributs supplémentaires en plus des paramètres déclarés du composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-201">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="ee834-202">Des attributs supplémentaires peuvent être capturés dans un dictionnaire *, puis* réintégrés à un élément lorsque le composant est rendu à l’aide de la [`@attributes`][3] Razor directive.</span><span class="sxs-lookup"><span data-stu-id="ee834-202">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="ee834-203">Ce scénario est utile lors de la définition d’un composant qui produit un élément de balisage qui prend en charge diverses personnalisations.</span><span class="sxs-lookup"><span data-stu-id="ee834-203">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="ee834-204">Par exemple, il peut être fastidieux de définir des attributs séparément pour un `<input>` qui prend en charge de nombreux paramètres.</span><span class="sxs-lookup"><span data-stu-id="ee834-204">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="ee834-205">Dans l’exemple suivant, le premier `<input>` élément ( `id="useIndividualParams"` ) utilise des paramètres de composant individuels, tandis que le deuxième `<input>` élément ( `id="useAttributesDict"` ) utilise la projection d’attributs :</span><span class="sxs-lookup"><span data-stu-id="ee834-205">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="ee834-206">Le type du paramètre doit implémenter `IEnumerable<KeyValuePair<string, object>>` avec des clés de chaîne.</span><span class="sxs-lookup"><span data-stu-id="ee834-206">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="ee834-207">L’utilisation `IReadOnlyDictionary<string, object>` de est également une option dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="ee834-207">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="ee834-208">Les éléments rendus `<input>` à l’aide des deux approches sont identiques :</span><span class="sxs-lookup"><span data-stu-id="ee834-208">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="ee834-209">Pour accepter des attributs arbitraires, définissez un paramètre de composant à l’aide de l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut avec la <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriété définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="ee834-209">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="ee834-210">La <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriété sur [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permet au paramètre de correspondre à tous les attributs qui ne correspondent à aucun autre paramètre.</span><span class="sxs-lookup"><span data-stu-id="ee834-210">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="ee834-211">Un composant ne peut définir qu’un seul paramètre avec <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="ee834-211">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="ee834-212">Le type de propriété utilisé avec <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> doit pouvoir être assigné à partir de `Dictionary<string, object>` avec des clés de chaîne.</span><span class="sxs-lookup"><span data-stu-id="ee834-212">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="ee834-213">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` sont également des options dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="ee834-213">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="ee834-214">La position [`@attributes`][3] relative à la position des attributs d’élément est importante.</span><span class="sxs-lookup"><span data-stu-id="ee834-214">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="ee834-215">Quand sont représentées [`@attributes`][3] sur l’élément, les attributs sont traités de droite à gauche (dernier à premier).</span><span class="sxs-lookup"><span data-stu-id="ee834-215">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="ee834-216">Prenons l’exemple suivant d’un composant qui consomme un `Child` composant :</span><span class="sxs-lookup"><span data-stu-id="ee834-216">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="ee834-217">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="ee834-218">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-218">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="ee834-219">L' `Child` attribut du composant `extra` est défini à droite de [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="ee834-219">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="ee834-220">Le `Parent` rendu du composant `<div>` contient `extra="5"` lorsqu’il est transmis via l’attribut supplémentaire, car les attributs sont traités de droite à gauche (dernier à premier) :</span><span class="sxs-lookup"><span data-stu-id="ee834-220">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="ee834-221">Dans l’exemple suivant, l’ordre de `extra` et [`@attributes`][3] est inversé dans le d' `Child` un composant `<div>` :</span><span class="sxs-lookup"><span data-stu-id="ee834-221">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="ee834-222">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-222">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="ee834-223">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-223">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="ee834-224">Le rendu `<div>` dans le `Parent` composant contient `extra="10"` lorsqu’il est passé par le biais de l’attribut supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="ee834-224">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="ee834-225">Capturer des références à des composants</span><span class="sxs-lookup"><span data-stu-id="ee834-225">Capture references to components</span></span>

<span data-ttu-id="ee834-226">Les références de composant offrent un moyen de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance, telles que `Show` ou `Reset` .</span><span class="sxs-lookup"><span data-stu-id="ee834-226">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="ee834-227">Pour capturer une référence de composant :</span><span class="sxs-lookup"><span data-stu-id="ee834-227">To capture a component reference:</span></span>

* <span data-ttu-id="ee834-228">Ajoutez un [`@ref`][4] attribut au composant enfant.</span><span class="sxs-lookup"><span data-stu-id="ee834-228">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="ee834-229">Définissez un champ avec le même type que le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="ee834-229">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="ee834-230">Lors du rendu du composant, le `loginDialog` champ est rempli avec l' `MyLoginDialog` instance du composant enfant.</span><span class="sxs-lookup"><span data-stu-id="ee834-230">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="ee834-231">Vous pouvez ensuite appeler des méthodes .NET sur l’instance du composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-231">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ee834-232">La `loginDialog` variable est remplie uniquement après le rendu du composant et sa sortie comprend l' `MyLoginDialog` élément.</span><span class="sxs-lookup"><span data-stu-id="ee834-232">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="ee834-233">Jusqu’à ce stade, il n’y a rien à référencer.</span><span class="sxs-lookup"><span data-stu-id="ee834-233">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="ee834-234">Pour manipuler des références de composants après la fin du rendu du composant, utilisez les [méthodes OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="ee834-234">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="ee834-235">Pour référencer des composants dans une boucle, consultez [capturer des références à plusieurs composants enfants similaires (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="ee834-235">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="ee834-236">Bien que la capture de références de composant utilise une syntaxe similaire pour [capturer des références d’élément](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), il ne s’agit pas d’une fonctionnalité JavaScript Interop.</span><span class="sxs-lookup"><span data-stu-id="ee834-236">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="ee834-237">Les références de composant ne sont pas passées au code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee834-237">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="ee834-238">Les références de composants sont uniquement utilisées dans le code .NET.</span><span class="sxs-lookup"><span data-stu-id="ee834-238">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="ee834-239">N’utilisez **pas** de références de composant pour muter l’état des composants enfants.</span><span class="sxs-lookup"><span data-stu-id="ee834-239">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="ee834-240">Utilisez plutôt des paramètres déclaratifs normaux pour passer des données aux composants enfants.</span><span class="sxs-lookup"><span data-stu-id="ee834-240">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="ee834-241">L’utilisation de paramètres déclaratifs normaux entraîne le rerendu automatique des composants enfants.</span><span class="sxs-lookup"><span data-stu-id="ee834-241">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="ee834-242">Appeler des méthodes de composant en externe pour mettre à jour l’État</span><span class="sxs-lookup"><span data-stu-id="ee834-242">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="ee834-243">utilise un contexte de synchronisation ( <xref:System.Threading.SynchronizationContext> ) pour appliquer un seul thread logique d’exécution.</span><span class="sxs-lookup"><span data-stu-id="ee834-243"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="ee834-244">Les méthodes de [cycle de vie](xref:blazor/lifecycle) d’un composant et les rappels d’événements déclenchés par Blazor sont exécutés sur le contexte de synchronisation.</span><span class="sxs-lookup"><span data-stu-id="ee834-244">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="ee834-245">Le contexte de synchronisation du serveur tente d’émuler un environnement monothread afin qu’il corresponde étroitement au modèle webassembly dans le navigateur, qui est mono-thread.</span><span class="sxs-lookup"><span data-stu-id="ee834-245"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="ee834-246">À un moment donné, le travail est effectué sur un seul thread, ce qui donne l’impression d’un seul thread logique.</span><span class="sxs-lookup"><span data-stu-id="ee834-246">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="ee834-247">Deux opérations ne sont pas exécutées simultanément.</span><span class="sxs-lookup"><span data-stu-id="ee834-247">No two operations execute concurrently.</span></span>

<span data-ttu-id="ee834-248">Dans le cas où un composant doit être mis à jour en fonction d’un événement externe, tel qu’un minuteur ou d’autres notifications, utilisez la `InvokeAsync` méthode, qui est distribuée au Blazor contexte de synchronisation de.</span><span class="sxs-lookup"><span data-stu-id="ee834-248">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="ee834-249">Par exemple, considérez un *service de notification* qui peut notifier n’importe quel composant d’écoute de l’État mis à jour :</span><span class="sxs-lookup"><span data-stu-id="ee834-249">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="ee834-250">Inscrivez le `NotifierService` en tant que singletion :</span><span class="sxs-lookup"><span data-stu-id="ee834-250">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="ee834-251">Dans Blazor Webassembly, inscrivez le service dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="ee834-251">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="ee834-252">Dans Blazor serveur, inscrivez le service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee834-252">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="ee834-253">Utilisez `NotifierService` pour mettre à jour un composant :</span><span class="sxs-lookup"><span data-stu-id="ee834-253">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="ee834-254">Dans l’exemple précédent, `NotifierService` appelle la méthode du composant `OnNotify` en dehors du Blazor contexte de synchronisation de.</span><span class="sxs-lookup"><span data-stu-id="ee834-254">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="ee834-255">`InvokeAsync`est utilisé pour basculer vers le contexte correct et pour la mise en file d’attente d’un rendu.</span><span class="sxs-lookup"><span data-stu-id="ee834-255">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="ee834-256">Utiliser \@ la clé pour contrôler la conservation des éléments et des composants</span><span class="sxs-lookup"><span data-stu-id="ee834-256">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="ee834-257">Lors du rendu d’une liste d’éléments ou de composants, et si les éléments ou les composants changent par la suite, l' Blazor algorithme de différenciation de doit décider quels éléments ou composants précédents peuvent être conservés et comment les objets de modèle doivent être mappés à eux.</span><span class="sxs-lookup"><span data-stu-id="ee834-257">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="ee834-258">Normalement, ce processus est automatique et peut être ignoré, mais dans certains cas, il peut être utile de contrôler le processus.</span><span class="sxs-lookup"><span data-stu-id="ee834-258">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="ee834-259">Prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ee834-259">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="ee834-260">Le contenu de la `People` collection peut changer avec des entrées insérées, supprimées ou réorganisées.</span><span class="sxs-lookup"><span data-stu-id="ee834-260">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="ee834-261">Lors du rerendu du composant, le `<DetailsEditor>` composant peut changer pour recevoir des `Details` valeurs de paramètre différentes.</span><span class="sxs-lookup"><span data-stu-id="ee834-261">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="ee834-262">Cela peut entraîner un rerendu plus complexe que prévu.</span><span class="sxs-lookup"><span data-stu-id="ee834-262">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="ee834-263">Dans certains cas, le rerendu peut entraîner des différences de comportement visibles, telles que le focus de l’élément perdu.</span><span class="sxs-lookup"><span data-stu-id="ee834-263">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="ee834-264">Le processus de mappage peut être contrôlé à l’aide de l' [`@key`][5] attribut directive.</span><span class="sxs-lookup"><span data-stu-id="ee834-264">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="ee834-265">[`@key`][5]force l’algorithme de comparaison à garantir la préservation des éléments ou des composants en fonction de la valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="ee834-265">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="ee834-266">Lorsque la `People` collection est modifiée, l’algorithme de comparaison conserve l’association entre les `<DetailsEditor>` instances et les `person` instances :</span><span class="sxs-lookup"><span data-stu-id="ee834-266">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="ee834-267">Si un `Person` est supprimé de la `People` liste, seule l' `<DetailsEditor>` instance correspondante est supprimée de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ee834-267">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="ee834-268">Les autres instances restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="ee834-268">Other instances are left unchanged.</span></span>
* <span data-ttu-id="ee834-269">Si un `Person` est inséré à une position dans la liste, une nouvelle `<DetailsEditor>` instance est insérée à cette position correspondante.</span><span class="sxs-lookup"><span data-stu-id="ee834-269">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="ee834-270">Les autres instances restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="ee834-270">Other instances are left unchanged.</span></span>
* <span data-ttu-id="ee834-271">Si `Person` les entrées sont réordonnées, les `<DetailsEditor>` instances correspondantes sont conservées et réordonnées dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ee834-271">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="ee834-272">Dans certains scénarios, l’utilisation de [`@key`][5] réduit la complexité du rerendu et évite les problèmes potentiels liés aux parties avec état de la modification du modèle DOM, telles que la position du focus.</span><span class="sxs-lookup"><span data-stu-id="ee834-272">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ee834-273">Les clés sont locales pour chaque élément ou composant conteneur.</span><span class="sxs-lookup"><span data-stu-id="ee834-273">Keys are local to each container element or component.</span></span> <span data-ttu-id="ee834-274">Les clés ne sont pas comparées globalement dans le document.</span><span class="sxs-lookup"><span data-stu-id="ee834-274">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="ee834-275">Quand utiliser la \@ clé</span><span class="sxs-lookup"><span data-stu-id="ee834-275">When to use \@key</span></span>

<span data-ttu-id="ee834-276">En règle générale, il est judicieux d’utiliser [`@key`][5] chaque fois qu’une liste est affichée (par exemple, dans un bloc [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) et qu’une valeur appropriée existe pour définir [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="ee834-276">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="ee834-277">Vous pouvez également utiliser [`@key`][5] pour empêcher Blazor de conserver un élément ou une sous-arborescence de composants lorsqu’un objet change :</span><span class="sxs-lookup"><span data-stu-id="ee834-277">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="ee834-278">En cas `@currentPerson` de modification, la [`@key`][5] directive Blazor d’attribut force à ignorer la totalité `<div>` et ses descendants, et à reconstruire la sous-arborescence de l’interface utilisateur avec de nouveaux éléments et composants.</span><span class="sxs-lookup"><span data-stu-id="ee834-278">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="ee834-279">Cela peut être utile si vous devez garantir qu’aucun État d’interface utilisateur n’est préservé lorsque des `@currentPerson` modifications sont apportées.</span><span class="sxs-lookup"><span data-stu-id="ee834-279">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="ee834-280">Quand ne pas utiliser la \@ clé</span><span class="sxs-lookup"><span data-stu-id="ee834-280">When not to use \@key</span></span>

<span data-ttu-id="ee834-281">Il y a un coût en matière de performances lors de la comparaison avec [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="ee834-281">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="ee834-282">Le coût des performances n’est pas important, mais spécifiez uniquement [`@key`][5] si les règles de conservation des éléments ou des composants bénéficient de l’application.</span><span class="sxs-lookup"><span data-stu-id="ee834-282">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="ee834-283">Même si [`@key`][5] n’est pas utilisé, Blazor conserve autant que possible les instances d’élément et de composant enfants.</span><span class="sxs-lookup"><span data-stu-id="ee834-283">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="ee834-284">Le seul avantage de l’utilisation de [`@key`][5] est de contrôler la *façon dont* les instances de modèle sont mappées aux instances de composant conservées, au lieu de l’algorithme de comparaison qui sélectionne le mappage.</span><span class="sxs-lookup"><span data-stu-id="ee834-284">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="ee834-285">Valeurs à utiliser pour la \@ clé</span><span class="sxs-lookup"><span data-stu-id="ee834-285">What values to use for \@key</span></span>

<span data-ttu-id="ee834-286">En règle générale, il est logique de fournir l’un des types de valeur suivants pour [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="ee834-286">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="ee834-287">Instances d’objet de modèle (par exemple, une `Person` instance comme dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="ee834-287">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="ee834-288">Cela garantit la préservation en fonction de l’égalité des références d’objet.</span><span class="sxs-lookup"><span data-stu-id="ee834-288">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="ee834-289">Identificateurs uniques (par exemple, les valeurs de clé primaire de type `int` , `string` ou `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="ee834-289">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="ee834-290">Vérifiez que les valeurs utilisées pour [`@key`][5] ne sont pas en conflit.</span><span class="sxs-lookup"><span data-stu-id="ee834-290">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="ee834-291">Si les valeurs en conflit sont détectées dans le même élément parent, Blazor lève une exception, car il ne peut pas mapper de manière déterministe les anciens éléments ou composants aux nouveaux éléments ou composants.</span><span class="sxs-lookup"><span data-stu-id="ee834-291">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="ee834-292">Utilisez uniquement des valeurs distinctes, telles que des instances d’objets ou des valeurs de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="ee834-292">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="ee834-293">Ne créez pas de composants qui écrivent dans leurs propres propriétés de paramètre</span><span class="sxs-lookup"><span data-stu-id="ee834-293">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="ee834-294">Les paramètres sont remplacés dans les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="ee834-294">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="ee834-295">Le contenu d’un composant enfant est rendu avec un <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="ee834-295">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="ee834-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé dans le composant parent.</span><span class="sxs-lookup"><span data-stu-id="ee834-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="ee834-297">Les paramètres sont réinitialisés, car le composant parent <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est restitué à nouveau lorsque est appelé et de nouvelles valeurs de paramètres sont fournies au composant enfant.</span><span class="sxs-lookup"><span data-stu-id="ee834-297">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="ee834-298">Prenons le `Expander` composant suivant :</span><span class="sxs-lookup"><span data-stu-id="ee834-298">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="ee834-299">Restitue le contenu enfant.</span><span class="sxs-lookup"><span data-stu-id="ee834-299">Renders child content.</span></span>
* <span data-ttu-id="ee834-300">Active ou désactive l’indication du contenu enfant avec un paramètre de composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-300">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="ee834-301">Le `Expander` composant est ajouté à un composant parent qui peut appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="ee834-301">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="ee834-302">Au départ, les `Expander` composants se comportent indépendamment lorsque leurs `Expanded` propriétés sont basculées.</span><span class="sxs-lookup"><span data-stu-id="ee834-302">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="ee834-303">Les composants enfants maintiennent leurs États comme prévu.</span><span class="sxs-lookup"><span data-stu-id="ee834-303">The child components maintain their states as expected.</span></span> <span data-ttu-id="ee834-304">Lorsque <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé dans le parent, le `Expanded` paramètre du premier composant enfant est réinitialisé à sa valeur initiale ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="ee834-304">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="ee834-305">La `Expander` valeur du deuxième composant `Expanded` n’est pas réinitialisée, car aucun contenu enfant n’est restitué dans le deuxième composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-305">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="ee834-306">Pour maintenir l’État dans le scénario précédent, utilisez un *champ privé* dans le `Expander` composant pour maintenir son état bascule.</span><span class="sxs-lookup"><span data-stu-id="ee834-306">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="ee834-307">Le `Expander` composant suivant :</span><span class="sxs-lookup"><span data-stu-id="ee834-307">The following `Expander` component:</span></span>

* <span data-ttu-id="ee834-308">Accepte la `Expanded` valeur de paramètre du composant à partir du parent.</span><span class="sxs-lookup"><span data-stu-id="ee834-308">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="ee834-309">Affecte la valeur du paramètre de composant à un *champ privé* ( `expanded` ) dans l' [événement OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="ee834-309">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="ee834-310">Utilise le champ privé pour conserver son état bascule interne.</span><span class="sxs-lookup"><span data-stu-id="ee834-310">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="ee834-311">Prise en charge des classes partielles</span><span class="sxs-lookup"><span data-stu-id="ee834-311">Partial class support</span></span>

Razor<span data-ttu-id="ee834-312">les composants sont générés en tant que classes partielles.</span><span class="sxs-lookup"><span data-stu-id="ee834-312"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="ee834-313">les composants sont créés à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="ee834-313"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="ee834-314">Le code C# est défini dans un [`@code`][1] bloc avec le balisage HTML et Razor le code dans un fichier unique.</span><span class="sxs-lookup"><span data-stu-id="ee834-314">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="ee834-315">les modèles définissent leurs Razor composants à l’aide de cette approche.</span><span class="sxs-lookup"><span data-stu-id="ee834-315"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="ee834-316">Le code C# est placé dans un fichier code-behind défini en tant que classe partielle.</span><span class="sxs-lookup"><span data-stu-id="ee834-316">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="ee834-317">L’exemple suivant montre le composant par défaut `Counter` avec un [`@code`][1] bloc dans une application générée à partir d’un Blazor modèle.</span><span class="sxs-lookup"><span data-stu-id="ee834-317">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="ee834-318">Le balisage HTML, le Razor code et le code C# se trouvent dans le même fichier :</span><span class="sxs-lookup"><span data-stu-id="ee834-318">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="ee834-319">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-319">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="ee834-320">Le `Counter` composant peut également être créé à l’aide d’un fichier code-behind avec une classe partielle :</span><span class="sxs-lookup"><span data-stu-id="ee834-320">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="ee834-321">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-321">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="ee834-322">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee834-322">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="ee834-323">Ajoutez tous les espaces de noms requis au fichier de classe partielle, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="ee834-323">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="ee834-324">Les espaces de noms standard utilisés par les Razor composants sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="ee834-324">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="ee834-325">Spécifier une classe de base</span><span class="sxs-lookup"><span data-stu-id="ee834-325">Specify a base class</span></span>

<span data-ttu-id="ee834-326">La [`@inherits`][6] directive peut être utilisée pour spécifier une classe de base pour un composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-326">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="ee834-327">L’exemple suivant montre comment un composant peut hériter d’une classe de base, `BlazorRocksBase` , pour fournir les propriétés et les méthodes du composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-327">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="ee834-328">La classe de base doit dériver de <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="ee834-328">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="ee834-329">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee834-329">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="ee834-330">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee834-330">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="ee834-331">Spécifier un attribut</span><span class="sxs-lookup"><span data-stu-id="ee834-331">Specify an attribute</span></span>

<span data-ttu-id="ee834-332">Les attributs peuvent être spécifiés dans Razor les composants à l’aide de la [`@attribute`][7] directive.</span><span class="sxs-lookup"><span data-stu-id="ee834-332">Attributes can be specified in Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="ee834-333">L’exemple suivant applique l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut à la classe de composant :</span><span class="sxs-lookup"><span data-stu-id="ee834-333">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="ee834-334">Importer des composants</span><span class="sxs-lookup"><span data-stu-id="ee834-334">Import components</span></span>

<span data-ttu-id="ee834-335">L’espace de noms d’un composant créé avec Razor est basé sur (par ordre de priorité) :</span><span class="sxs-lookup"><span data-stu-id="ee834-335">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="ee834-336">[`@namespace`][8]désignation dans le Razor balisage de fichier (*. Razor*) ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="ee834-336">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="ee834-337">Du projet `RootNamespace` dans le fichier projet ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="ee834-337">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="ee834-338">Nom du projet, pris à partir du nom de fichier du fichier projet (*. csproj*), et chemin d’accès de la racine du projet au composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-338">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="ee834-339">Par exemple, le Framework résout *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) en espace de noms `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="ee834-339">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="ee834-340">Les composants suivent les règles de liaison de noms C#.</span><span class="sxs-lookup"><span data-stu-id="ee834-340">Components follow C# name binding rules.</span></span> <span data-ttu-id="ee834-341">Pour le `Index` composant dans cet exemple, les composants de l’étendue sont tous les composants :</span><span class="sxs-lookup"><span data-stu-id="ee834-341">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="ee834-342">Dans le même dossier, *pages*.</span><span class="sxs-lookup"><span data-stu-id="ee834-342">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="ee834-343">Composants de la racine du projet qui ne spécifient pas explicitement un espace de noms différent.</span><span class="sxs-lookup"><span data-stu-id="ee834-343">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="ee834-344">Les composants définis dans un espace de noms différent sont placés dans la portée à l’aide Razor de [`@using`][2] la directive de.</span><span class="sxs-lookup"><span data-stu-id="ee834-344">Components defined in a different namespace are brought into scope using Razor's [`@using`][2] directive.</span></span>

<span data-ttu-id="ee834-345">Si un autre composant, `NavMenu.razor` , existe dans le dossier *BlazorSample/Shared/* , le composant peut être utilisé dans `Index.razor` avec l' [`@using`][2] instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="ee834-345">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following [`@using`][2] statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="ee834-346">Les composants peuvent également être référencés à l’aide de leurs noms complets, ce qui ne requiert pas la [`@using`][2] directive :</span><span class="sxs-lookup"><span data-stu-id="ee834-346">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="ee834-347">La `global::` qualification n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ee834-347">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="ee834-348">L’importation de composants avec des instructions [using](/dotnet/csharp/language-reference/keywords/using-statement) avec alias (par exemple, `@using Foo = Bar` ) n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ee834-348">Importing components with aliased [using](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="ee834-349">Les noms partiellement qualifiés ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ee834-349">Partially qualified names aren't supported.</span></span> <span data-ttu-id="ee834-350">Par exemple, `@using BlazorSample` l’ajout et la référencement du `NavMenu` composant ( `NavMenu.razor` ) avec `<Shared.NavMenu></Shared.NavMenu>` ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ee834-350">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="ee834-351">Attributs d’éléments HTML conditionnels</span><span class="sxs-lookup"><span data-stu-id="ee834-351">Conditional HTML element attributes</span></span>

<span data-ttu-id="ee834-352">Les attributs des éléments HTML sont rendus de manière conditionnelle en fonction de la valeur .NET.</span><span class="sxs-lookup"><span data-stu-id="ee834-352">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="ee834-353">Si la valeur est `false` ou `null` , l’attribut n’est pas rendu.</span><span class="sxs-lookup"><span data-stu-id="ee834-353">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="ee834-354">Si la valeur est `true` , l’attribut est rendu réduit.</span><span class="sxs-lookup"><span data-stu-id="ee834-354">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="ee834-355">Dans l’exemple suivant, `IsCompleted` détermine si `checked` est rendu dans le balisage de l’élément :</span><span class="sxs-lookup"><span data-stu-id="ee834-355">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="ee834-356">Si `IsCompleted` est `true` , la case à cocher s’affiche comme suit :</span><span class="sxs-lookup"><span data-stu-id="ee834-356">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="ee834-357">Si `IsCompleted` est `false` , la case à cocher s’affiche comme suit :</span><span class="sxs-lookup"><span data-stu-id="ee834-357">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="ee834-358">Pour plus d'informations, consultez <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="ee834-358">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="ee834-359">Certains attributs HTML, tels que [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), ne fonctionnent pas correctement quand le type .net est `bool` .</span><span class="sxs-lookup"><span data-stu-id="ee834-359">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="ee834-360">Dans ce cas, utilisez un `string` type au lieu d’un `bool` .</span><span class="sxs-lookup"><span data-stu-id="ee834-360">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="ee834-361">HTML brut</span><span class="sxs-lookup"><span data-stu-id="ee834-361">Raw HTML</span></span>

<span data-ttu-id="ee834-362">Les chaînes sont normalement rendues à l’aide de nœuds de texte DOM, ce qui signifie que tout balisage qu’elles peuvent contenir est ignorée et traitée comme du texte littéral.</span><span class="sxs-lookup"><span data-stu-id="ee834-362">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="ee834-363">Pour afficher le code HTML brut, encapsulez le contenu HTML dans une `MarkupString` valeur.</span><span class="sxs-lookup"><span data-stu-id="ee834-363">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="ee834-364">La valeur est analysée au format HTML ou SVG et est insérée dans le DOM.</span><span class="sxs-lookup"><span data-stu-id="ee834-364">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="ee834-365">Le rendu de code HTML brut construit à partir de n’importe quelle source non approuvée constitue un risque pour la **sécurité** et doit être évité !</span><span class="sxs-lookup"><span data-stu-id="ee834-365">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="ee834-366">L’exemple suivant illustre l’utilisation du `MarkupString` type pour ajouter un bloc de contenu HTML statique à la sortie rendue d’un composant :</span><span class="sxs-lookup"><span data-stu-id="ee834-366">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="ee834-367">Valeurs et paramètres en cascade</span><span class="sxs-lookup"><span data-stu-id="ee834-367">Cascading values and parameters</span></span>

<span data-ttu-id="ee834-368">Dans certains scénarios, il est peu commode de transmettre des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composant](#component-parameters), en particulier lorsqu’il existe plusieurs couches de composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-368">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="ee834-369">Les valeurs et paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant ancêtre de fournir une valeur à tous ses composants descendants.</span><span class="sxs-lookup"><span data-stu-id="ee834-369">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="ee834-370">Les valeurs et les paramètres en cascade fournissent également une approche pour les composants à coordonner.</span><span class="sxs-lookup"><span data-stu-id="ee834-370">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="ee834-371">Exemple de thème</span><span class="sxs-lookup"><span data-stu-id="ee834-371">Theme example</span></span>

<span data-ttu-id="ee834-372">Dans l’exemple suivant tiré de l’exemple d’application, la `ThemeInfo` classe spécifie les informations de thème pour descendre dans la hiérarchie des composants, de sorte que tous les boutons d’une partie donnée de l’application partagent le même style.</span><span class="sxs-lookup"><span data-stu-id="ee834-372">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="ee834-373">*UIThemeClasses/themeinfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="ee834-373">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="ee834-374">Un composant ancêtre peut fournir une valeur en cascade à l’aide du composant de valeur en cascade.</span><span class="sxs-lookup"><span data-stu-id="ee834-374">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="ee834-375">Le <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant encapsule une sous-arborescence de la hiérarchie des composants et fournit une valeur unique à tous les composants de cette sous-arborescence.</span><span class="sxs-lookup"><span data-stu-id="ee834-375">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="ee834-376">Par exemple, l’exemple d’application spécifie les informations de thème ( `ThemeInfo` ) dans l’une des dispositions de l’application en tant que paramètre en cascade pour tous les composants qui composent le corps de la disposition de la `@Body` propriété.</span><span class="sxs-lookup"><span data-stu-id="ee834-376">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="ee834-377">`ButtonClass`la valeur est affectée `btn-success` à dans le composant Layout.</span><span class="sxs-lookup"><span data-stu-id="ee834-377">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="ee834-378">Tout composant descendant peut consommer cette propriété par le biais de l' `ThemeInfo` objet en cascade.</span><span class="sxs-lookup"><span data-stu-id="ee834-378">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="ee834-379">`CascadingValuesParametersLayout`-</span><span class="sxs-lookup"><span data-stu-id="ee834-379">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="ee834-380">Pour utiliser des valeurs en cascade, les composants déclarent des paramètres en cascade à l’aide de l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="ee834-380">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="ee834-381">Les valeurs en cascade sont liées aux paramètres en cascade par type.</span><span class="sxs-lookup"><span data-stu-id="ee834-381">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="ee834-382">Dans l’exemple d’application, le `CascadingValuesParametersTheme` composant lie la `ThemeInfo` valeur en cascade à un paramètre en cascade.</span><span class="sxs-lookup"><span data-stu-id="ee834-382">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="ee834-383">Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-383">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="ee834-384">`CascadingValuesParametersTheme`-</span><span class="sxs-lookup"><span data-stu-id="ee834-384">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="ee834-385">Pour mettre en cascade plusieurs valeurs du même type dans la même sous-arborescence, fournissez une <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> chaîne unique à chaque <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant et à son [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut correspondant.</span><span class="sxs-lookup"><span data-stu-id="ee834-385">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="ee834-386">Dans l’exemple suivant, deux <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composants montent en cascade différentes instances de `MyCascadingType` par nom :</span><span class="sxs-lookup"><span data-stu-id="ee834-386">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="ee834-387">Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans le composant ancêtre par nom :</span><span class="sxs-lookup"><span data-stu-id="ee834-387">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="ee834-388">Exemple TabSet</span><span class="sxs-lookup"><span data-stu-id="ee834-388">TabSet example</span></span>

<span data-ttu-id="ee834-389">Les paramètres en cascade permettent également aux composants de collaborer au sein de la hiérarchie des composants.</span><span class="sxs-lookup"><span data-stu-id="ee834-389">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="ee834-390">Par exemple, considérez l’exemple *TabSet* suivant dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="ee834-390">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="ee834-391">L’exemple d’application possède une `ITab` interface qui implémente les onglets suivants :</span><span class="sxs-lookup"><span data-stu-id="ee834-391">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="ee834-392">Le `CascadingValuesParametersTabSet` composant utilise le `TabSet` composant, qui contient plusieurs `Tab` composants :</span><span class="sxs-lookup"><span data-stu-id="ee834-392">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="ee834-393">Les `Tab` composants enfants ne sont pas explicitement passés comme paramètres à `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="ee834-393">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="ee834-394">Au lieu de cela, les `Tab` composants enfants font partie du contenu enfant de `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="ee834-394">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="ee834-395">Toutefois, le `TabSet` doit toujours connaître chaque `Tab` composant pour pouvoir afficher les en-têtes et l’onglet actif. Pour activer cette coordination sans nécessiter de code supplémentaire, le `TabSet` composant *peut se présenter comme une valeur en cascade* qui est ensuite récupérée par les `Tab` composants descendants.</span><span class="sxs-lookup"><span data-stu-id="ee834-395">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="ee834-396">`TabSet`-</span><span class="sxs-lookup"><span data-stu-id="ee834-396">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="ee834-397">Les composants descendants `Tab` capturent le contenant `TabSet` comme paramètre en cascade, de sorte que les `Tab` composants s’ajoutent eux-mêmes à la et à la `TabSet` coordonnée sur laquelle l’onglet est actif.</span><span class="sxs-lookup"><span data-stu-id="ee834-397">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="ee834-398">`Tab`-</span><span class="sxs-lookup"><span data-stu-id="ee834-398">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="ee834-399">ceux</span><span class="sxs-lookup"><span data-stu-id="ee834-399"> templates</span></span>

<span data-ttu-id="ee834-400">Les fragments de rendu peuvent être définis à l’aide de la Razor syntaxe de modèle.</span><span class="sxs-lookup"><span data-stu-id="ee834-400">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="ee834-401">les modèles sont un moyen de définir un extrait de code d’interface utilisateur et de supposer le format suivant :</span><span class="sxs-lookup"><span data-stu-id="ee834-401"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="ee834-402">L’exemple suivant montre comment spécifier <xref:Microsoft.AspNetCore.Components.RenderFragment> des valeurs et <xref:Microsoft.AspNetCore.Components.RenderFragment%601> et restituer des modèles directement dans un composant.</span><span class="sxs-lookup"><span data-stu-id="ee834-402">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="ee834-403">Les fragments de rendu peuvent également être passés comme arguments à des [composants basés](xref:blazor/templated-components)sur un modèle.</span><span class="sxs-lookup"><span data-stu-id="ee834-403">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="ee834-404">Sortie rendue du code précédent :</span><span class="sxs-lookup"><span data-stu-id="ee834-404">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="ee834-405">Images SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="ee834-405">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="ee834-406">Étant donné que Blazor le rendu des images HTML, prises en charge par le navigateur, y compris les images SVG (Scalable Vector Graphics) (*. svg*), est pris en charge via la `<img>` balise :</span><span class="sxs-lookup"><span data-stu-id="ee834-406">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="ee834-407">De même, les images SVG sont prises en charge dans les règles CSS d’un fichier de feuille de style (*. CSS*) :</span><span class="sxs-lookup"><span data-stu-id="ee834-407">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="ee834-408">Toutefois, le balisage SVG en ligne n’est pas pris en charge dans tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="ee834-408">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="ee834-409">Si vous placez une `<svg>` balise directement dans un fichier de composant (*. Razor*), le rendu d’image de base est pris en charge, mais de nombreux scénarios avancés ne sont pas encore pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ee834-409">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="ee834-410">Par exemple, les `<use>` balises ne sont pas actuellement respectées et [`@bind`][10] ne peuvent pas être utilisées avec certaines balises SVG.</span><span class="sxs-lookup"><span data-stu-id="ee834-410">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="ee834-411">Pour plus d’informations, consultez [prise en charge SVG dans Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="ee834-411">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee834-412">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ee834-412">Additional resources</span></span>

* <span data-ttu-id="ee834-413"><xref:security/blazor/server/threat-mitigation>: Fournit des conseils sur la création d' Blazor applications serveur qui doivent être en concurrence avec l’épuisement des ressources.</span><span class="sxs-lookup"><span data-stu-id="ee834-413"><xref:security/blazor/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
