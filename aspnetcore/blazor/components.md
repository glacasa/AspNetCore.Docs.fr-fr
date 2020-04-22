---
title: Créer et utiliser ASP.NET composants Core Razor
author: guardrex
description: Apprenez à créer et à utiliser des composants Razor, y compris comment se lier aux données, gérer les événements et gérer les cycles de vie des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791489"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="584ce-103">Créer et utiliser ASP.NET composants Core Razor</span><span class="sxs-lookup"><span data-stu-id="584ce-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="584ce-104">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), et Tobias [Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="584ce-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="584ce-105">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="584ce-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="584ce-106">applications sont construites à l’aide *de composants*.</span><span class="sxs-lookup"><span data-stu-id="584ce-106"> apps are built using *components*.</span></span> <span data-ttu-id="584ce-107">Un composant est un morceau autonome de l’interface utilisateur (UI), comme une page, un dialogue ou une forme.</span><span class="sxs-lookup"><span data-stu-id="584ce-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="584ce-108">Un composant comprend la balisage HTML et la logique de traitement nécessaire pour injecter des données ou répondre aux événements de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="584ce-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="584ce-109">Les composants sont flexibles et légers.</span><span class="sxs-lookup"><span data-stu-id="584ce-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="584ce-110">Ils peuvent être imbriqués, réutilisés et partagés entre les projets.</span><span class="sxs-lookup"><span data-stu-id="584ce-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="584ce-111">Classes de composants</span><span class="sxs-lookup"><span data-stu-id="584ce-111">Component classes</span></span>

<span data-ttu-id="584ce-112">Les composants sont implémentés dans les fichiers de composants [Razor](xref:mvc/views/razor) (*.razor*) à l’aide d’une combinaison de balisage C et HTML.</span><span class="sxs-lookup"><span data-stu-id="584ce-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="584ce-113">Un composant Blazor est officiellement appelé un *composant Razor*.</span><span class="sxs-lookup"><span data-stu-id="584ce-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="584ce-114">Le nom d’un composant doit commencer par un caractère majuscule.</span><span class="sxs-lookup"><span data-stu-id="584ce-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="584ce-115">Par exemple, *MyCoolComponent.razor* est valide, et *myCoolComponent.razor* est invalide.</span><span class="sxs-lookup"><span data-stu-id="584ce-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="584ce-116">L’interface utilisateur pour un composant est définie à l’aide de HTML.</span><span class="sxs-lookup"><span data-stu-id="584ce-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="584ce-117">La logique de rendu dynamique (par exemple, les boucles, conditions, expressions) est ajoutée à l’aide d’une syntaxe C# intégrée appelée [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="584ce-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="584ce-118">Lorsqu’une application est compilée, la logique de balisage HTML et de rendu C est convertie en classe de composants.</span><span class="sxs-lookup"><span data-stu-id="584ce-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="584ce-119">Le nom de la classe générée correspond au nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="584ce-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="584ce-120">Les membres de la classe de composants sont définis dans un bloc `@code`.</span><span class="sxs-lookup"><span data-stu-id="584ce-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="584ce-121">Dans `@code` le bloc, l’état des composants (propriétés, champs) est spécifié avec des méthodes de manipulation des événements ou pour définir d’autres logiques de composants.</span><span class="sxs-lookup"><span data-stu-id="584ce-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="584ce-122">Plus d’un bloc `@code` est autorisé.</span><span class="sxs-lookup"><span data-stu-id="584ce-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="584ce-123">Les membres de composant peuvent être employés dans le cadre de `@`la logique de rendu du composant en utilisant des expressions de C 'qui commencent par .</span><span class="sxs-lookup"><span data-stu-id="584ce-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="584ce-124">Par exemple, un champ C est rendu `@` en préfixant le nom du champ.</span><span class="sxs-lookup"><span data-stu-id="584ce-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="584ce-125">L’exemple suivant évalue et rend :</span><span class="sxs-lookup"><span data-stu-id="584ce-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="584ce-126">`_headingFontStyle`à la valeur de `font-style`propriété CSS pour .</span><span class="sxs-lookup"><span data-stu-id="584ce-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="584ce-127">`_headingText`au contenu de `<h1>` l’élément.</span><span class="sxs-lookup"><span data-stu-id="584ce-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="584ce-128">Une fois le composant rendu au départ, le composant régénére son arbre de rendu en réponse aux événements.</span><span class="sxs-lookup"><span data-stu-id="584ce-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="584ce-129">compare ensuite le nouvel arbre de rendu par rapport au précédent et applique toutes les modifications au modèle d’objet document (DOM) du navigateur.</span><span class="sxs-lookup"><span data-stu-id="584ce-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="584ce-130">Les composants sont des classes ordinaires de C et peuvent être placés n’importe où dans un projet.</span><span class="sxs-lookup"><span data-stu-id="584ce-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="584ce-131">Les composants qui produisent des pages Web résident généralement dans le dossier *Pages.*</span><span class="sxs-lookup"><span data-stu-id="584ce-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="584ce-132">Les composants non-pages sont fréquemment placés dans le dossier *partagé* ou dans un dossier personnalisé ajouté au projet.</span><span class="sxs-lookup"><span data-stu-id="584ce-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="584ce-133">En règle générale, l’espace de nom d’un composant est dérivé de l’espace de nom racine de l’application et de l’emplacement (dossier) du composant dans l’application.</span><span class="sxs-lookup"><span data-stu-id="584ce-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="584ce-134">Si l’espace de nom `BlazorApp` de `Counter` racine de l’application est et le composant réside dans le dossier *Pages* :</span><span class="sxs-lookup"><span data-stu-id="584ce-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="584ce-135">L’espace `Counter` de nom `BlazorApp.Pages`du composant est .</span><span class="sxs-lookup"><span data-stu-id="584ce-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="584ce-136">Le nom de type entièrement `BlazorApp.Pages.Counter`qualifié du composant est .</span><span class="sxs-lookup"><span data-stu-id="584ce-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="584ce-137">Pour plus d’informations, consultez la section [composants d’importation.](#import-components)</span><span class="sxs-lookup"><span data-stu-id="584ce-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="584ce-138">Pour utiliser un dossier personnalisé, ajoutez l’espace de nom du dossier personnalisé au composant parent ou au fichier *_Imports.razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="584ce-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="584ce-139">Par exemple, l’espace de nom suivant met les composants dans un dossier `BlazorApp`de composants *disponibles* lorsque l’espace de nom de racine de l’application est :</span><span class="sxs-lookup"><span data-stu-id="584ce-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="584ce-140">Les ressources statiques</span><span class="sxs-lookup"><span data-stu-id="584ce-140">Static assets</span></span>

Blazor<span data-ttu-id="584ce-141">suit la convention de ASP.NET applications Core plaçant des actifs statiques sous le [dossier web root (wwwroot)](xref:fundamentals/index#web-root)du projet.</span><span class="sxs-lookup"><span data-stu-id="584ce-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="584ce-142">Utilisez un chemin de`/`base-relatif ( ) pour se référer à la racine web pour un actif statique.</span><span class="sxs-lookup"><span data-stu-id="584ce-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="584ce-143">Dans l’exemple suivant, *logo.png* est physiquement situé dans le dossier *'PROJECT ROOT'/wwwroot/images:*</span><span class="sxs-lookup"><span data-stu-id="584ce-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="584ce-144">Les composants de rasoir **ne** prennent`~/`pas en charge la notation tilde-slash ( ).</span><span class="sxs-lookup"><span data-stu-id="584ce-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="584ce-145">Pour plus d’informations sur la <xref:host-and-deploy/blazor/index#app-base-path>définition du chemin de base d’une application, voir .</span><span class="sxs-lookup"><span data-stu-id="584ce-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="584ce-146">Tag Helpers ne sont pas pris en charge dans les composants</span><span class="sxs-lookup"><span data-stu-id="584ce-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="584ce-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) ne sont pas pris en charge dans les composants Razor *(fichiers .razor).*</span><span class="sxs-lookup"><span data-stu-id="584ce-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="584ce-148">Pour fournir des fonctionnalités de Blazortype Tag Helper, créez un composant avec les mêmes fonctionnalités que l’aide Tag et utilisez le composant à la place.</span><span class="sxs-lookup"><span data-stu-id="584ce-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="584ce-149">Utiliser des composants</span><span class="sxs-lookup"><span data-stu-id="584ce-149">Use components</span></span>

<span data-ttu-id="584ce-150">Les composants peuvent inclure d’autres composants en les déclarant à l’aide de la syntaxe d’éléments HTML.</span><span class="sxs-lookup"><span data-stu-id="584ce-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="584ce-151">Le balisage pour l’utilisation d’un composant ressemble à une balise HTML où le nom de la balise est le type du composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="584ce-152">La majoration suivante dans *Index.razor* rend un `HeadingComponent` cas :</span><span class="sxs-lookup"><span data-stu-id="584ce-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="584ce-153">*Composants/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="584ce-154">Si un composant contient un élément HTML avec une première lettre majuscule qui ne correspond pas à un nom de composant, un avertissement est émis indiquant que l’élément a un nom inattendu.</span><span class="sxs-lookup"><span data-stu-id="584ce-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="584ce-155">L’ajout d’une `@using` directive pour l’espace nom du composant rend le composant disponible, ce qui résout l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="584ce-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="584ce-156">Routage</span><span class="sxs-lookup"><span data-stu-id="584ce-156">Routing</span></span>

<span data-ttu-id="584ce-157">L’itinéraire Blazor est réalisé en fournissant un modèle d’itinéraire à chaque composant accessible de l’application.</span><span class="sxs-lookup"><span data-stu-id="584ce-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="584ce-158">Lorsqu’un fichier `@page` Razor avec directive est compilé, <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> la classe générée reçoit un modèle d’itinéraire précisant.</span><span class="sxs-lookup"><span data-stu-id="584ce-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="584ce-159">Au moment de l’exécution, le `RouteAttribute` routeur recherche des classes de composants avec un composant et rend n’importe quel composant a un modèle d’itinéraire qui correspond à l’URL demandée.</span><span class="sxs-lookup"><span data-stu-id="584ce-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="584ce-160">Pour plus d’informations, consultez <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="584ce-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="584ce-161">Paramètres</span><span class="sxs-lookup"><span data-stu-id="584ce-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="584ce-162">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="584ce-162">Route parameters</span></span>

<span data-ttu-id="584ce-163">Les composants peuvent recevoir les paramètres d’itinéraire à partir du modèle d’itinéraire fourni dans la `@page` directive.</span><span class="sxs-lookup"><span data-stu-id="584ce-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="584ce-164">Le routeur utilise les paramètres d’itinéraire pour remplir les paramètres de composant correspondants.</span><span class="sxs-lookup"><span data-stu-id="584ce-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="584ce-165">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="584ce-166">Les paramètres facultatifs ne `@page` sont pas pris en charge, de sorte que deux directives sont appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="584ce-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="584ce-167">Le premier permet la navigation sur le composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="584ce-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="584ce-168">La `@page` deuxième directive `{text}` reçoit le paramètre d’itinéraire et attribue la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="584ce-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="584ce-169">La syntaxe de`*`/`**`paramètre *fourre-tout* ( qui capture le chemin à travers plusieurs limites de pliage, **n’est pas** prise en charge dans les composants Razor (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="584ce-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="584ce-170">Paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="584ce-170">Component parameters</span></span>

<span data-ttu-id="584ce-171">Les composants peuvent avoir des paramètres de composant , qui `[Parameter]` sont *définis*en utilisant des propriétés publiques sur la classe de composant avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="584ce-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="584ce-172">Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.</span><span class="sxs-lookup"><span data-stu-id="584ce-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="584ce-173">*Composants/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="584ce-174">Dans l’exemple suivant de `ParentComponent` l’application d’échantillon, la valeur de la `Title` propriété de la `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="584ce-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="584ce-175">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="584ce-176">Ne créez pas de composants qui écrivent à leurs propres *paramètres de composant,* utilisez plutôt un champ privé.</span><span class="sxs-lookup"><span data-stu-id="584ce-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="584ce-177">Pour plus d’informations, voir le [Don’t create components qui écrivent à leur propre section propriétés de paramètres.](#dont-create-components-that-write-to-their-own-parameter-properties)</span><span class="sxs-lookup"><span data-stu-id="584ce-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="584ce-178">Contenu pour enfants</span><span class="sxs-lookup"><span data-stu-id="584ce-178">Child content</span></span>

<span data-ttu-id="584ce-179">Les composants peuvent définir le contenu d’un autre composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-179">Components can set the content of another component.</span></span> <span data-ttu-id="584ce-180">Le composant d’attribution fournit le contenu entre les balises qui spécifient le composant de réception.</span><span class="sxs-lookup"><span data-stu-id="584ce-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="584ce-181">Dans l’exemple `ChildComponent` suivant, `ChildContent` le a `RenderFragment`une propriété qui représente un , qui représente un segment de l’interface utilisateur à rendre.</span><span class="sxs-lookup"><span data-stu-id="584ce-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="584ce-182">La valeur `ChildContent` de est positionnée dans le balisage du composant où le contenu doit être rendu.</span><span class="sxs-lookup"><span data-stu-id="584ce-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="584ce-183">La valeur `ChildContent` de est reçue de la composante mère et `panel-body`rendue à l’intérieur du panneau Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="584ce-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="584ce-184">*Composants/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="584ce-185">La propriété `RenderFragment` qui reçoit `ChildContent` le contenu doit être nommée par convention.</span><span class="sxs-lookup"><span data-stu-id="584ce-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="584ce-186">L’application `ParentComponent` de l’échantillon peut `ChildComponent` fournir du contenu `<ChildComponent>` pour rendre le contenu en plaçant le contenu à l’intérieur des balises.</span><span class="sxs-lookup"><span data-stu-id="584ce-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="584ce-187">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="584ce-188">Attribuer des éclaboussures et des paramètres arbitraires</span><span class="sxs-lookup"><span data-stu-id="584ce-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="584ce-189">Les composants peuvent capturer et rendre des attributs supplémentaires en plus des paramètres déclarés du composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="584ce-190">D’autres attributs peuvent être capturés dans un dictionnaire, puis *éclaboussés* sur un élément lorsque le composant est rendu à l’aide de la [`@attributes`](xref:mvc/views/razor#attributes) directive Razor.</span><span class="sxs-lookup"><span data-stu-id="584ce-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="584ce-191">Ce scénario est utile lors de la définition d’un composant qui produit un élément de balisage qui prend en charge une variété de personnalisations.</span><span class="sxs-lookup"><span data-stu-id="584ce-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="584ce-192">Par exemple, il peut être fastidieux de `<input>` définir les attributs séparément pour un qui prend en charge de nombreux paramètres.</span><span class="sxs-lookup"><span data-stu-id="584ce-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="584ce-193">Dans l’exemple suivant, `<input>` `id="useIndividualParams"`le premier élément ( ) `<input>` utilise`id="useAttributesDict"`des paramètres de composant individuels, tandis que le deuxième élément ( ) utilise l’éclaboussure d’attribut :</span><span class="sxs-lookup"><span data-stu-id="584ce-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="584ce-194">Le type de paramètre doit être implémenté `IEnumerable<KeyValuePair<string, object>>` avec des touches de chaîne.</span><span class="sxs-lookup"><span data-stu-id="584ce-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="584ce-195">L’utilisation `IReadOnlyDictionary<string, object>` est également une option dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="584ce-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="584ce-196">Les éléments `<input>` rendus à l’aide des deux approches sont identiques :</span><span class="sxs-lookup"><span data-stu-id="584ce-196">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="584ce-197">Pour accepter des attributs arbitraires, `[Parameter]` définissez `CaptureUnmatchedValues` un paramètre de composant à l’aide de l’attribut avec la propriété définie à `true`:</span><span class="sxs-lookup"><span data-stu-id="584ce-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="584ce-198">La `CaptureUnmatchedValues` propriété `[Parameter]` sur permet au paramètre de correspondre à tous les attributs qui ne correspondent à aucun autre paramètre.</span><span class="sxs-lookup"><span data-stu-id="584ce-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="584ce-199">Un composant ne peut définir `CaptureUnmatchedValues`un seul paramètre avec .</span><span class="sxs-lookup"><span data-stu-id="584ce-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="584ce-200">Le type de `CaptureUnmatchedValues` propriété utilisé `Dictionary<string, object>` avec doit être assignable à partir de clés de chaîne.</span><span class="sxs-lookup"><span data-stu-id="584ce-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="584ce-201">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` sont également des options dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="584ce-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="584ce-202">La position `@attributes` des attributs relatifs à la position des attributs d’élément est importante.</span><span class="sxs-lookup"><span data-stu-id="584ce-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="584ce-203">Lorsque `@attributes` sont éclaboussés sur l’élément, les attributs sont traités de droite à gauche (du dernier au premier).</span><span class="sxs-lookup"><span data-stu-id="584ce-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="584ce-204">Prenons l’exemple suivant d’un `Child` composant qui consomme un composant :</span><span class="sxs-lookup"><span data-stu-id="584ce-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="584ce-205">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="584ce-206">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="584ce-207">L’attribut `Child` `extra` du composant est réglé `@attributes`au droit de .</span><span class="sxs-lookup"><span data-stu-id="584ce-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="584ce-208">Le `Parent` composant rendu `<div>` contient `extra="5"` lorsqu’il est passé par l’attribut supplémentaire parce que les attributs sont traités de droite à gauche (dernier à d’abord):</span><span class="sxs-lookup"><span data-stu-id="584ce-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="584ce-209">Dans l’exemple suivant, `extra` `@attributes` l’ordre et `Child` l’inverse dans le composant `<div>`:</span><span class="sxs-lookup"><span data-stu-id="584ce-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="584ce-210">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="584ce-211">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="584ce-212">Le rendu `<div>` dans `Parent` le `extra="10"` composant contient une fois passé par l’attribut supplémentaire:</span><span class="sxs-lookup"><span data-stu-id="584ce-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="584ce-213">Capturez les références aux composants</span><span class="sxs-lookup"><span data-stu-id="584ce-213">Capture references to components</span></span>

<span data-ttu-id="584ce-214">Les références de composants fournissent un moyen de référencer une instance `Show` de `Reset`composant afin que vous puissiez émettre des commandes à cette instance, telle que ou .</span><span class="sxs-lookup"><span data-stu-id="584ce-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="584ce-215">Pour saisir une référence de composant :</span><span class="sxs-lookup"><span data-stu-id="584ce-215">To capture a component reference:</span></span>

* <span data-ttu-id="584ce-216">Ajoutez [`@ref`](xref:mvc/views/razor#ref) un attribut à la composante enfant.</span><span class="sxs-lookup"><span data-stu-id="584ce-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="584ce-217">Définissez un champ avec le même type que la composante enfant.</span><span class="sxs-lookup"><span data-stu-id="584ce-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="584ce-218">Lorsque le composant est `_loginDialog` rendu, le champ `MyLoginDialog` est peuplé avec l’instance de composant de l’enfant.</span><span class="sxs-lookup"><span data-stu-id="584ce-218">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="584ce-219">Vous pouvez alors invoquer des méthodes .NET sur l’instance du composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="584ce-220">La `_loginDialog` variable n’est peuplée qu’après la remise `MyLoginDialog` du composant et sa sortie comprend l’élément.</span><span class="sxs-lookup"><span data-stu-id="584ce-220">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="584ce-221">Jusque-là, il n’y a rien à faire référence.</span><span class="sxs-lookup"><span data-stu-id="584ce-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="584ce-222">Pour manipuler les références de composants après que le composant a terminé le rendu, utilisez les [méthodes OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="584ce-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="584ce-223">Pour référencer les composants d’une boucle, voir [les références Capture à plusieurs composants similaires pour enfants (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="584ce-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="584ce-224">Bien que la capture des références de composants utilise une syntaxe similaire à [la capture des références d’éléments,](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)ce n’est pas une fonction d’interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="584ce-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="584ce-225">Les références de composants ne&mdash;sont pas passées au code JavaScript qu’elles ne sont utilisées que dans le code .NET.</span><span class="sxs-lookup"><span data-stu-id="584ce-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="584ce-226">N’utilisez **pas** de références de composants pour muter l’état des composants pour enfants.</span><span class="sxs-lookup"><span data-stu-id="584ce-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="584ce-227">Utilisez plutôt des paramètres déclaratifs normaux pour transmettre des données aux composants de l’enfant.</span><span class="sxs-lookup"><span data-stu-id="584ce-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="584ce-228">L’utilisation de paramètres déclaratifs normaux entraîne des composants pour enfants qui rerender aux bons moments automatiquement.</span><span class="sxs-lookup"><span data-stu-id="584ce-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="584ce-229">Invoquer des méthodes de composants à l’extérieur pour mettre à jour l’état</span><span class="sxs-lookup"><span data-stu-id="584ce-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="584ce-230">utilise `SynchronizationContext` un pour faire respecter un seul fil logique de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="584ce-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="584ce-231">Les méthodes du cycle de [vie d’un](xref:blazor/lifecycle) composant Blazor et les `SynchronizationContext`rappels d’événements qui sont soulevés par sont exécutés à ce sujet .</span><span class="sxs-lookup"><span data-stu-id="584ce-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="584ce-232">Dans le cas où un composant doit être mis à jour en fonction `InvokeAsync` d’un événement Blazorexterne, comme une minuterie ou d’autres notifications, utiliser la méthode, qui sera expédiée à 's `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="584ce-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="584ce-233">Par exemple, envisagez un *service de notificateur* qui peut aviser tout élément d’écoute de l’état mis à jour :</span><span class="sxs-lookup"><span data-stu-id="584ce-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="584ce-234">Enregistrez-vous `NotifierService` comme une seuletion:</span><span class="sxs-lookup"><span data-stu-id="584ce-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="584ce-235">Dans Blazor WebAssembly, inscrivez `Program.Main`le service dans :</span><span class="sxs-lookup"><span data-stu-id="584ce-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="584ce-236">Dans Blazor Server, enregistrez `Startup.ConfigureServices`le service dans :</span><span class="sxs-lookup"><span data-stu-id="584ce-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="584ce-237">Utilisez `NotifierService` le pour mettre à jour un composant:</span><span class="sxs-lookup"><span data-stu-id="584ce-237">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="584ce-238">Dans l’exemple `NotifierService` précédent, invoque la méthode du `OnNotify` composant en dehors de Blazor's `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="584ce-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="584ce-239">`InvokeAsync`est utilisé pour passer au contexte correct et faire la queue d’un rendu.</span><span class="sxs-lookup"><span data-stu-id="584ce-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="584ce-240">Utiliser \@la clé pour contrôler la préservation des éléments et des composants</span><span class="sxs-lookup"><span data-stu-id="584ce-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="584ce-241">Lors du rendu d’une liste d’éléments ou de Blazorcomposants et les éléments ou composants changent par la suite, l’algorithme de diffing doit décider lequel des éléments ou composants précédents peut être conservé et comment les objets modèles doivent les cartographier.</span><span class="sxs-lookup"><span data-stu-id="584ce-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="584ce-242">Normalement, ce processus est automatique et peut être ignoré, mais il ya des cas où vous pouvez contrôler le processus.</span><span class="sxs-lookup"><span data-stu-id="584ce-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="584ce-243">Prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="584ce-243">Consider the following example:</span></span>

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

<span data-ttu-id="584ce-244">Le contenu `People` de la collection peut changer avec des entrées insérées, supprimées ou réédiffues.</span><span class="sxs-lookup"><span data-stu-id="584ce-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="584ce-245">Lorsque le composant rerenders, le `<DetailsEditor>` composant `Details` peut changer pour recevoir différentes valeurs de paramètres.</span><span class="sxs-lookup"><span data-stu-id="584ce-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="584ce-246">Cela peut entraîner un rerendering plus complexe que prévu.</span><span class="sxs-lookup"><span data-stu-id="584ce-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="584ce-247">Dans certains cas, le rerendering peut conduire à des différences de comportement visibles, telles que la concentration d’éléments perdus.</span><span class="sxs-lookup"><span data-stu-id="584ce-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="584ce-248">Le processus de cartographie peut [`@key`](xref:mvc/views/razor#key) être contrôlé avec l’attribut de directive.</span><span class="sxs-lookup"><span data-stu-id="584ce-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="584ce-249">`@key`oblige l’algorithme à garantir la préservation d’éléments ou de composants en fonction de la valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="584ce-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="584ce-250">Lorsque `People` la collection change, l’algorithme de `<DetailsEditor>` diffing conserve l’association entre les instances et `person` les instances :</span><span class="sxs-lookup"><span data-stu-id="584ce-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="584ce-251">Si `Person` un a est `People` supprimé de `<DetailsEditor>` la liste, seule l’instance correspondante est supprimée de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="584ce-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="584ce-252">D’autres cas sont laissés inchangés.</span><span class="sxs-lookup"><span data-stu-id="584ce-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="584ce-253">Si `Person` un est inséré à une position `<DetailsEditor>` dans la liste, un nouvel exemple est inséré à cette position correspondante.</span><span class="sxs-lookup"><span data-stu-id="584ce-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="584ce-254">D’autres cas sont laissés inchangés.</span><span class="sxs-lookup"><span data-stu-id="584ce-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="584ce-255">Si `Person` les entrées sont réin commandées, les instances correspondantes `<DetailsEditor>` sont conservées et réinservées dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="584ce-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="584ce-256">Dans certains scénarios, `@key` l’utilisation de minimise la complexité du rerendering et évite les problèmes potentiels avec les parties étatiques du DOM changeant, telles que la position de mise au point.</span><span class="sxs-lookup"><span data-stu-id="584ce-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="584ce-257">Les clés sont locales à chaque élément ou composant de conteneur.</span><span class="sxs-lookup"><span data-stu-id="584ce-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="584ce-258">Les clés ne sont pas comparées globalement dans l’ensemble du document.</span><span class="sxs-lookup"><span data-stu-id="584ce-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="584ce-259">Quand utiliser \@la clé</span><span class="sxs-lookup"><span data-stu-id="584ce-259">When to use \@key</span></span>

<span data-ttu-id="584ce-260">Typiquement, il est `@key` logique d’utiliser chaque fois qu’une liste est rendue (par exemple, dans un `@foreach` bloc) et une valeur appropriée existe pour définir le `@key`.</span><span class="sxs-lookup"><span data-stu-id="584ce-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="584ce-261">Vous pouvez `@key` également Blazor utiliser pour empêcher de préserver un élément ou un sous-arbre composant lorsqu’un objet change :</span><span class="sxs-lookup"><span data-stu-id="584ce-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="584ce-262">En `@currentPerson` cas `@key` de modification, la directive d’attribut oblige Blazor à jeter l’ensemble `<div>` et ses descendants et à reconstruire le sous-arbre au sein de l’interface utilisateur avec de nouveaux éléments et composants.</span><span class="sxs-lookup"><span data-stu-id="584ce-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="584ce-263">Cela peut être utile si vous avez besoin de `@currentPerson` garantir qu’aucun état d’interface utilisateur n’est préservé lorsque les changements.</span><span class="sxs-lookup"><span data-stu-id="584ce-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="584ce-264">Quand ne \@pas utiliser la clé</span><span class="sxs-lookup"><span data-stu-id="584ce-264">When not to use \@key</span></span>

<span data-ttu-id="584ce-265">Il ya un coût de performance `@key`lors de diffing avec .</span><span class="sxs-lookup"><span data-stu-id="584ce-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="584ce-266">Le coût de performance n’est `@key` pas élevé, mais ne précise que si le contrôle de l’élément ou des règles de préservation des composants bénéficient à l’application.</span><span class="sxs-lookup"><span data-stu-id="584ce-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="584ce-267">Même `@key` s’il n’est pas utilisé, Blazor préserve autant que possible les instances d’éléments et d’éléments pour enfants.</span><span class="sxs-lookup"><span data-stu-id="584ce-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="584ce-268">Le seul avantage `@key` à utiliser est le contrôle sur la *façon dont* les instances du modèle sont cartographiés aux instances des composants préservés, au lieu de l’algorithme de diffing sélectionnant la cartographie.</span><span class="sxs-lookup"><span data-stu-id="584ce-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="584ce-269">Quelles valeurs utiliser \@pour la clé</span><span class="sxs-lookup"><span data-stu-id="584ce-269">What values to use for \@key</span></span>

<span data-ttu-id="584ce-270">En général, il est logique de fournir `@key`l’un des types suivants de valeur pour :</span><span class="sxs-lookup"><span data-stu-id="584ce-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="584ce-271">Exemple d’objets modèles `Person` (par exemple, un cas comme dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="584ce-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="584ce-272">Cela garantit la préservation basée sur l’égalité de référence des objets.</span><span class="sxs-lookup"><span data-stu-id="584ce-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="584ce-273">Identifiants uniques (par exemple, valeurs `int` `string`clés `Guid`primaires de type, , ou ).</span><span class="sxs-lookup"><span data-stu-id="584ce-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="584ce-274">Assurez-vous que `@key` les valeurs utilisées pour ne pas s’opposer.</span><span class="sxs-lookup"><span data-stu-id="584ce-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="584ce-275">Si des valeurs contradictoires sont détectées Blazor dans le même élément parent, jette une exception parce qu’elle ne peut pas cartographier de façon déterministe de vieux éléments ou composants à de nouveaux éléments ou composants.</span><span class="sxs-lookup"><span data-stu-id="584ce-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="584ce-276">N’utilisez que des valeurs distinctes, telles que des instances d’objets ou des valeurs clés primaires.</span><span class="sxs-lookup"><span data-stu-id="584ce-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="584ce-277">Ne créez pas de composants qui écrivent à leurs propres propriétés de paramètres</span><span class="sxs-lookup"><span data-stu-id="584ce-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="584ce-278">Les paramètres sont dépassés dans les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="584ce-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="584ce-279">Le contenu d’un composant enfant `RenderFragment`est rendu avec un .</span><span class="sxs-lookup"><span data-stu-id="584ce-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="584ce-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé dans la composante parente.</span><span class="sxs-lookup"><span data-stu-id="584ce-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="584ce-281">Les paramètres sont réinitialisés <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> parce que le composant parent rerenders quand est appelé et de nouvelles valeurs de paramètres sont fournis à la composante enfant.</span><span class="sxs-lookup"><span data-stu-id="584ce-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="584ce-282">Considérez `Expander` le composant suivant qui :</span><span class="sxs-lookup"><span data-stu-id="584ce-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="584ce-283">Rend le contenu de l’enfant.</span><span class="sxs-lookup"><span data-stu-id="584ce-283">Renders child content.</span></span>
* <span data-ttu-id="584ce-284">Bascules montrant le contenu de l’enfant avec un paramètre de composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-284">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="584ce-285">Le `Expander` composant est ajouté à un `StateHasChanged`composant parent qui peut appeler :</span><span class="sxs-lookup"><span data-stu-id="584ce-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="584ce-286">Initialement, `Expander` les composants se comportent `Expanded` indépendamment lorsque leurs propriétés sont basculer.</span><span class="sxs-lookup"><span data-stu-id="584ce-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="584ce-287">Les composants de l’enfant maintiennent leurs états comme prévu.</span><span class="sxs-lookup"><span data-stu-id="584ce-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="584ce-288">Lorsqu’il `StateHasChanged` est appelé `Expanded` chez le parent, le paramètre du`true`premier composant enfant est réinitialisé à sa valeur initiale ( ).</span><span class="sxs-lookup"><span data-stu-id="584ce-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="584ce-289">La `Expander` valeur du `Expanded` deuxième composant n’est pas réinitialisée parce qu’aucun contenu pour enfants n’est rendu dans le deuxième composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="584ce-290">Pour maintenir l’état dans le scénario `Expander` précédent, utilisez un champ *privé* dans la composante pour maintenir son état basculeux.</span><span class="sxs-lookup"><span data-stu-id="584ce-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="584ce-291">Le `Expander` composant suivant :</span><span class="sxs-lookup"><span data-stu-id="584ce-291">The following `Expander` component:</span></span>

* <span data-ttu-id="584ce-292">Accepte la `Expanded` valeur du paramètre de composant du parent.</span><span class="sxs-lookup"><span data-stu-id="584ce-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="584ce-293">Attribue la valeur du paramètre`_expanded`de composant à un champ *privé* ( ) dans [l’événement OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="584ce-293">Assigns the component parameter value to a *private field* (`_expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="584ce-294">Utilise le champ privé pour maintenir son état de basculement interne.</span><span class="sxs-lookup"><span data-stu-id="584ce-294">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="584ce-295">Soutien partiel de la classe</span><span class="sxs-lookup"><span data-stu-id="584ce-295">Partial class support</span></span>

<span data-ttu-id="584ce-296">Les composants de rasoir sont générés en tant que classes partielles.</span><span class="sxs-lookup"><span data-stu-id="584ce-296">Razor components are generated as partial classes.</span></span> <span data-ttu-id="584ce-297">Les composants razor sont rédigés à l’aide de l’une ou l’autre des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="584ce-297">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="584ce-298">Le code C est [`@code`](xref:mvc/views/razor#code) défini dans un bloc avec le balisage HTML et le code Razor dans un seul fichier.</span><span class="sxs-lookup"><span data-stu-id="584ce-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="584ce-299">définissent leurs composants Razor à l’aide de cette approche.</span><span class="sxs-lookup"><span data-stu-id="584ce-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="584ce-300">Le code C est placé dans un fichier de code-derrière défini comme une classe partielle.</span><span class="sxs-lookup"><span data-stu-id="584ce-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="584ce-301">L’exemple suivant `Counter` montre le `@code` composant par défaut Blazor avec un bloc dans une application générée à partir d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="584ce-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="584ce-302">Le balisage HTML, le code Razor et le code C sont dans le même fichier :</span><span class="sxs-lookup"><span data-stu-id="584ce-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="584ce-303">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-303">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="584ce-304">Le `Counter` composant peut également être créé à l’aide d’un fichier de code-derrière avec une classe partielle:</span><span class="sxs-lookup"><span data-stu-id="584ce-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="584ce-305">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="584ce-306">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="584ce-306">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="584ce-307">Ajoutez tous les espaces de nom requis au fichier de classe partielle au besoin.</span><span class="sxs-lookup"><span data-stu-id="584ce-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="584ce-308">Les espaces nominaux typiques utilisés par les composants Razor comprennent :</span><span class="sxs-lookup"><span data-stu-id="584ce-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="584ce-309">Spécifier une classe de base</span><span class="sxs-lookup"><span data-stu-id="584ce-309">Specify a base class</span></span>

<span data-ttu-id="584ce-310">La [`@inherits`](xref:mvc/views/razor#inherits) directive peut être utilisée pour spécifier une classe de base pour un composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="584ce-311">L’exemple suivant montre comment un composant `BlazorRocksBase`peut hériter d’une classe de base, pour fournir les propriétés et les méthodes du composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="584ce-312">La classe de `ComponentBase`base devrait dériver de .</span><span class="sxs-lookup"><span data-stu-id="584ce-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="584ce-313">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="584ce-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="584ce-314">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="584ce-314">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="584ce-315">Spécifier un attribut</span><span class="sxs-lookup"><span data-stu-id="584ce-315">Specify an attribute</span></span>

<span data-ttu-id="584ce-316">Les attributs peuvent être spécifiés [`@attribute`](xref:mvc/views/razor#attribute) dans les composants Razor avec la directive.</span><span class="sxs-lookup"><span data-stu-id="584ce-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="584ce-317">L’exemple suivant `[Authorize]` applique l’attribut à la classe des composants :</span><span class="sxs-lookup"><span data-stu-id="584ce-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="584ce-318">Composants d’importation</span><span class="sxs-lookup"><span data-stu-id="584ce-318">Import components</span></span>

<span data-ttu-id="584ce-319">Le namespace d’un composant rédigé avec Razor est basé sur (dans l’ordre prioritaire):</span><span class="sxs-lookup"><span data-stu-id="584ce-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="584ce-320">[`@namespace`](xref:mvc/views/razor#namespace)désignation dans le fichier Razor (`@namespace BlazorSample.MyNamespace`*.razor*) balisage ( ).</span><span class="sxs-lookup"><span data-stu-id="584ce-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="584ce-321">Le projet `RootNamespace` est dans le`<RootNamespace>BlazorSample</RootNamespace>`dossier du projet ( ).</span><span class="sxs-lookup"><span data-stu-id="584ce-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="584ce-322">Le nom du projet, tiré du nom de fichier du fichier du projet *(.csproj*), et le chemin de la racine du projet à la composante.</span><span class="sxs-lookup"><span data-stu-id="584ce-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="584ce-323">Par exemple, le cadre résout *à* l’espace*BlazorSample.csproj* `BlazorSample.Pages`nom .</span><span class="sxs-lookup"><span data-stu-id="584ce-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="584ce-324">Les composants suivent les règles contraignantes du nom C.</span><span class="sxs-lookup"><span data-stu-id="584ce-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="584ce-325">Pour `Index` le composant de cet exemple, les composantes de portée sont tous les composants :</span><span class="sxs-lookup"><span data-stu-id="584ce-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="584ce-326">Dans le même dossier, *Pages*.</span><span class="sxs-lookup"><span data-stu-id="584ce-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="584ce-327">Les composants de la racine du projet qui ne spécifient pas explicitement un espace de nom différent.</span><span class="sxs-lookup"><span data-stu-id="584ce-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="584ce-328">Les composants définis dans un espace de nom [`@using`](xref:mvc/views/razor#using) différent sont mis en portée à l’aide de la directive de Razor.</span><span class="sxs-lookup"><span data-stu-id="584ce-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="584ce-329">Si un `NavMenu.razor`autre composant, , existe dans le *BlazorSample/Shared/* `Index.razor` dossier, `@using` le composant peut être utilisé avec l’instruction suivante:</span><span class="sxs-lookup"><span data-stu-id="584ce-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="584ce-330">Les composants peuvent également être référencés à l’aide de leurs noms entièrement qualifiés, ce qui n’exige pas la [`@using`](xref:mvc/views/razor#using) directive :</span><span class="sxs-lookup"><span data-stu-id="584ce-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="584ce-331">La `global::` qualification n’est pas soutenue.</span><span class="sxs-lookup"><span data-stu-id="584ce-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="584ce-332">L’importation de composants `using` avec des `@using Foo = Bar`relevés alias (par exemple, ) n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="584ce-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="584ce-333">Les noms partiellement qualifiés ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="584ce-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="584ce-334">Par exemple, `@using BlazorSample` l’ajout et le référencement `NavMenu.razor` avec `<Shared.NavMenu></Shared.NavMenu>` n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="584ce-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="584ce-335">Attributs conditionnels d’élément HTML</span><span class="sxs-lookup"><span data-stu-id="584ce-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="584ce-336">Les attributs d’élément HTML sont rendus sous condition en fonction de la valeur .NET.</span><span class="sxs-lookup"><span data-stu-id="584ce-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="584ce-337">Si la `false` valeur `null`est ou, l’attribut n’est pas rendu.</span><span class="sxs-lookup"><span data-stu-id="584ce-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="584ce-338">Si la `true`valeur est, l’attribut est rendu minimisé.</span><span class="sxs-lookup"><span data-stu-id="584ce-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="584ce-339">Dans l’exemple `IsCompleted` suivant, `checked` détermine si elle est rendue dans la majoration de l’élément :</span><span class="sxs-lookup"><span data-stu-id="584ce-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="584ce-340">Si `IsCompleted` `true`c’est le cas, la case à cocher est rendue comme:</span><span class="sxs-lookup"><span data-stu-id="584ce-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="584ce-341">Si `IsCompleted` `false`c’est le cas, la case à cocher est rendue comme:</span><span class="sxs-lookup"><span data-stu-id="584ce-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="584ce-342">Pour plus d’informations, consultez <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="584ce-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="584ce-343">Certains attributs HTML, tels que [aria-pressé,](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)ne fonctionnent pas correctement `bool`lorsque le type .NET est un .</span><span class="sxs-lookup"><span data-stu-id="584ce-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="584ce-344">Dans ces cas, `string` utilisez un `bool`type au lieu d’un .</span><span class="sxs-lookup"><span data-stu-id="584ce-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="584ce-345">HTML brut</span><span class="sxs-lookup"><span data-stu-id="584ce-345">Raw HTML</span></span>

<span data-ttu-id="584ce-346">Les cordes sont normalement rendues à l’aide de nœuds texte DOM, ce qui signifie que toute balisage qu’ils peuvent contenir est ignorée et traitée comme un texte littéral.</span><span class="sxs-lookup"><span data-stu-id="584ce-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="584ce-347">Pour rendre HTML brut, enveloppez `MarkupString` le contenu HTML dans une valeur.</span><span class="sxs-lookup"><span data-stu-id="584ce-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="584ce-348">La valeur est analysée sous forme de HTML ou de SVG et insérée dans le DOM.</span><span class="sxs-lookup"><span data-stu-id="584ce-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="584ce-349">Rendre HTML brut construit à partir de n’importe quelle source non fiable est un **risque pour la sécurité** et doit être évité!</span><span class="sxs-lookup"><span data-stu-id="584ce-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="584ce-350">L’exemple suivant `MarkupString` montre l’utilisation du type pour ajouter un bloc de contenu HTML statique à la sortie rendue d’un composant :</span><span class="sxs-lookup"><span data-stu-id="584ce-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="584ce-351">Valeurs et paramètres en cascade</span><span class="sxs-lookup"><span data-stu-id="584ce-351">Cascading values and parameters</span></span>

<span data-ttu-id="584ce-352">Dans certains scénarios, il est gênant de faire circuler des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composants,](#component-parameters)surtout lorsqu’il y a plusieurs couches de composants.</span><span class="sxs-lookup"><span data-stu-id="584ce-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="584ce-353">Les valeurs et les paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant d’ancêtre de fournir une valeur à tous ses composants descendants.</span><span class="sxs-lookup"><span data-stu-id="584ce-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="584ce-354">Les valeurs et les paramètres en cascade offrent également une approche pour les composants à coordonner.</span><span class="sxs-lookup"><span data-stu-id="584ce-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="584ce-355">Exemple de thème</span><span class="sxs-lookup"><span data-stu-id="584ce-355">Theme example</span></span>

<span data-ttu-id="584ce-356">Dans l’exemple suivant de `ThemeInfo` l’application d’échantillon, la classe spécifie les informations thématiques pour circuler dans la hiérarchie des composants afin que tous les boutons d’une partie donnée de l’application partagent le même style.</span><span class="sxs-lookup"><span data-stu-id="584ce-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="584ce-357">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="584ce-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="584ce-358">Un composant d’ancêtre peut fournir une valeur en cascade à l’aide du composant valeur en cascade.</span><span class="sxs-lookup"><span data-stu-id="584ce-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="584ce-359">Le `CascadingValue` composant enveloppe un sous-ensemble de la hiérarchie des composants et fournit une valeur unique à tous les composants de ce sous-arbre.</span><span class="sxs-lookup"><span data-stu-id="584ce-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="584ce-360">Par exemple, l’application de l’échantillon spécifie les informations thématiques (`ThemeInfo`) dans l’une des `@Body` mises en page de l’application comme paramètre en cascade pour tous les composants qui composent le corps de mise en page de la propriété.</span><span class="sxs-lookup"><span data-stu-id="584ce-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="584ce-361">`ButtonClass`se voit attribuer `btn-success` une valeur de la composante de mise en page.</span><span class="sxs-lookup"><span data-stu-id="584ce-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="584ce-362">N’importe quel composant descendant `ThemeInfo` peut consommer cette propriété par l’objet en cascade.</span><span class="sxs-lookup"><span data-stu-id="584ce-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="584ce-363">`CascadingValuesParametersLayout`Composant:</span><span class="sxs-lookup"><span data-stu-id="584ce-363">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="584ce-364">Pour utiliser les valeurs en cascade, les composants déclarent `[CascadingParameter]` les paramètres en cascade à l’aide de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="584ce-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="584ce-365">Les valeurs en cascade sont liées aux paramètres en cascade par type.</span><span class="sxs-lookup"><span data-stu-id="584ce-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="584ce-366">Dans l’application `CascadingValuesParametersTheme` d’échantillon, `ThemeInfo` le composant lie la valeur en cascade à un paramètre en cascade.</span><span class="sxs-lookup"><span data-stu-id="584ce-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="584ce-367">Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="584ce-368">`CascadingValuesParametersTheme`Composant:</span><span class="sxs-lookup"><span data-stu-id="584ce-368">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

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
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="584ce-369">Pour en cascader plusieurs valeurs du même type `Name` dans le `CascadingValue` même sous-arbre, fournir une chaîne unique à chaque composant et son correspondant `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="584ce-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="584ce-370">Dans l’exemple `CascadingValue` suivant, deux composants `MyCascadingType` cascadent différents exemples de par nom:</span><span class="sxs-lookup"><span data-stu-id="584ce-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="584ce-371">Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans la composante ancêtre par leur nom :</span><span class="sxs-lookup"><span data-stu-id="584ce-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="584ce-372">Exemple de TabSet</span><span class="sxs-lookup"><span data-stu-id="584ce-372">TabSet example</span></span>

<span data-ttu-id="584ce-373">Les paramètres en cascade permettent également aux composants de collaborer à travers la hiérarchie des composants.</span><span class="sxs-lookup"><span data-stu-id="584ce-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="584ce-374">Par exemple, considérez l’exemple *TabSet* suivant dans l’application d’échantillon.</span><span class="sxs-lookup"><span data-stu-id="584ce-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="584ce-375">L’application d’échantillon a une `ITab` interface qui implémente des onglets :</span><span class="sxs-lookup"><span data-stu-id="584ce-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="584ce-376">Le `CascadingValuesParametersTabSet` composant `TabSet` utilise le composant, qui contient plusieurs `Tab` composants :</span><span class="sxs-lookup"><span data-stu-id="584ce-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="584ce-377">Les `Tab` composants de l’enfant ne sont `TabSet`pas explicitement adoptés comme paramètres de la .</span><span class="sxs-lookup"><span data-stu-id="584ce-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="584ce-378">Au lieu `Tab` de cela, les composants de `TabSet`l’enfant font partie du contenu de l’enfant de la .</span><span class="sxs-lookup"><span data-stu-id="584ce-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="584ce-379">Cependant, `TabSet` le besoin doit `Tab` encore connaître chaque composant afin qu’il puisse rendre les en-têtes et l’onglet actif. Pour permettre cette coordination sans `TabSet` nécessiter de code supplémentaire, le composant *peut se fournir comme une valeur en cascade* qui est ensuite captée par les composants descendants. `Tab`</span><span class="sxs-lookup"><span data-stu-id="584ce-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="584ce-380">`TabSet`Composant:</span><span class="sxs-lookup"><span data-stu-id="584ce-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="584ce-381">Les `Tab` composants descendants `TabSet` capturent le contenant comme `Tab` paramètre en cascade, de sorte que les composants s’ajoutent à l’onglet `TabSet` et coordonnent sur quel onglet est actif.</span><span class="sxs-lookup"><span data-stu-id="584ce-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="584ce-382">`Tab`Composant:</span><span class="sxs-lookup"><span data-stu-id="584ce-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="584ce-383">Modèles de rasoir</span><span class="sxs-lookup"><span data-stu-id="584ce-383">Razor templates</span></span>

<span data-ttu-id="584ce-384">Les fragments de rendu peuvent être définis à l’aide de la syntaxe du modèle Razor.</span><span class="sxs-lookup"><span data-stu-id="584ce-384">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="584ce-385">Les modèles de rasoir sont un moyen de définir un extrait d’interface utilisateur et d’assumer le format suivant :</span><span class="sxs-lookup"><span data-stu-id="584ce-385">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="584ce-386">L’exemple suivant illustre `RenderFragment` comment `RenderFragment<T>` spécifier et valeurs et rendre les modèles directement dans un composant.</span><span class="sxs-lookup"><span data-stu-id="584ce-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="584ce-387">Les fragments de rendu peuvent également être adoptés comme arguments aux [composants modélistes.](xref:blazor/templated-components)</span><span class="sxs-lookup"><span data-stu-id="584ce-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="584ce-388">Sortie rendue du code précédent :</span><span class="sxs-lookup"><span data-stu-id="584ce-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="584ce-389">Images évolutives de Vector Graphics (SVG)</span><span class="sxs-lookup"><span data-stu-id="584ce-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="584ce-390">Depuis Blazor rend HTML, les images prises en charge par navigateur, y compris les images Scalable Vector Graphics (SVG) (*.svg*), sont prises en charge via le `<img>` tag :</span><span class="sxs-lookup"><span data-stu-id="584ce-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="584ce-391">De même, les images SVG sont prises en charge dans les règles CSS d’un fichier de feuille de style (*.css*):</span><span class="sxs-lookup"><span data-stu-id="584ce-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="584ce-392">Cependant, la balisage SVG en ligne n’est pas pris en charge dans tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="584ce-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="584ce-393">Si vous `<svg>` placez une balise directement dans un fichier composant (*.razor*), le rendu d’image de base est pris en charge, mais de nombreux scénarios avancés ne sont pas encore pris en charge.</span><span class="sxs-lookup"><span data-stu-id="584ce-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="584ce-394">Par exemple, `<use>` les balises `@bind` ne sont pas actuellement respectées et ne peuvent pas être utilisées avec certaines balises SVG.</span><span class="sxs-lookup"><span data-stu-id="584ce-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="584ce-395">Nous prévoyons répondre à ces limites dans une version future.</span><span class="sxs-lookup"><span data-stu-id="584ce-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="584ce-396">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="584ce-396">Additional resources</span></span>

* <span data-ttu-id="584ce-397"><xref:security/blazor/server>&ndash; Inclut des Blazor conseils sur la création d’applications Server qui doivent faire face à l’épuisement des ressources.</span><span class="sxs-lookup"><span data-stu-id="584ce-397"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
