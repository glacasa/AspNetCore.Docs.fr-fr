---
title: Créer et utiliser des composants ASP.NET Core Razor
author: guardrex
description: Découvrez comment créer et utiliser des composants Razor, notamment comment lier des données, gérer des événements et gérer des cycles de vie de composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: a9ae84c36716bfc07ae3cf86214e48ad24770401
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205954"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="13feb-103">Créer et utiliser des composants ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="13feb-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="13feb-104">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="13feb-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="13feb-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13feb-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="13feb-106">les applications sont générées à l’aide de *composants*.</span><span class="sxs-lookup"><span data-stu-id="13feb-106"> apps are built using *components*.</span></span> <span data-ttu-id="13feb-107">Un composant est un bloc d’interface utilisateur (IU) autonome, tel qu’une page, une boîte de dialogue ou un formulaire.</span><span class="sxs-lookup"><span data-stu-id="13feb-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="13feb-108">Un composant comprend le balisage HTML et la logique de traitement requise pour injecter des données ou répondre à des événements d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="13feb-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="13feb-109">Les composants sont flexibles et légers.</span><span class="sxs-lookup"><span data-stu-id="13feb-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="13feb-110">Elles peuvent être imbriquées, réutilisées et partagées entre les projets.</span><span class="sxs-lookup"><span data-stu-id="13feb-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="13feb-111">Classes de composant</span><span class="sxs-lookup"><span data-stu-id="13feb-111">Component classes</span></span>

<span data-ttu-id="13feb-112">Les composants sont implémentés dans les fichiers de composants [Razor](xref:mvc/views/razor) (*. Razor*) à l’aide d’une combinaison de balisage C# et html.</span><span class="sxs-lookup"><span data-stu-id="13feb-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="13feb-113">Un composant dans Blazor est officiellement désigné sous le terme de *composant Razor*.</span><span class="sxs-lookup"><span data-stu-id="13feb-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="13feb-114">Le nom d’un composant doit commencer par un caractère majuscule.</span><span class="sxs-lookup"><span data-stu-id="13feb-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="13feb-115">Par exemple, *MyCoolComponent. Razor* est valide et *MyCoolComponent. Razor* n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="13feb-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="13feb-116">L’interface utilisateur d’un composant est définie à l’aide de HTML.</span><span class="sxs-lookup"><span data-stu-id="13feb-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="13feb-117">La logique de rendu dynamique (par exemple, les boucles, conditions, expressions) est ajoutée à l’aide d’une syntaxe C# intégrée appelée [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="13feb-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="13feb-118">Quand une application est compilée, le balisage HTML et la logique de rendu C# sont convertis en une classe de composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="13feb-119">Le nom de la classe générée correspond au nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="13feb-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="13feb-120">Les membres de la classe de composants sont définis dans un bloc `@code`.</span><span class="sxs-lookup"><span data-stu-id="13feb-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="13feb-121">Dans le `@code` bloc, l’état du composant (propriétés, champs) est spécifié avec des méthodes pour la gestion des événements ou pour la définition d’une autre logique de composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="13feb-122">Plus d’un bloc `@code` est autorisé.</span><span class="sxs-lookup"><span data-stu-id="13feb-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="13feb-123">Les membres de composant peuvent être utilisés dans le cadre de la logique de rendu du composant à l' `@`aide d’expressions C# qui commencent par.</span><span class="sxs-lookup"><span data-stu-id="13feb-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="13feb-124">Par exemple, un champ C# est rendu en préfixant `@` le nom du champ.</span><span class="sxs-lookup"><span data-stu-id="13feb-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="13feb-125">L’exemple suivant évalue et affiche :</span><span class="sxs-lookup"><span data-stu-id="13feb-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="13feb-126">`_headingFontStyle`à la valeur de propriété CSS `font-style`pour.</span><span class="sxs-lookup"><span data-stu-id="13feb-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="13feb-127">`_headingText`au contenu de l' `<h1>` élément.</span><span class="sxs-lookup"><span data-stu-id="13feb-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="13feb-128">Une fois le composant restitué initialement, le composant régénère son arborescence de rendu en réponse aux événements.</span><span class="sxs-lookup"><span data-stu-id="13feb-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="13feb-129">compare ensuite la nouvelle arborescence de rendu à la précédente et applique toutes les modifications apportées au Document Object Model du navigateur (DOM).</span><span class="sxs-lookup"><span data-stu-id="13feb-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="13feb-130">Les composants sont des classes C# ordinaires qui peuvent être placées n’importe où dans un projet.</span><span class="sxs-lookup"><span data-stu-id="13feb-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="13feb-131">Les composants qui produisent des pages Web résident généralement dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="13feb-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="13feb-132">Les composants qui ne sont pas des pages sont souvent placés dans le dossier *partagé* ou dans un dossier personnalisé ajouté au projet.</span><span class="sxs-lookup"><span data-stu-id="13feb-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="13feb-133">En règle générale, l’espace de noms d’un composant est dérivé de l’espace de noms racine de l’application et de l’emplacement (dossier) du composant dans l’application.</span><span class="sxs-lookup"><span data-stu-id="13feb-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="13feb-134">Si l’espace de noms racine de `BlazorApp` l’application `Counter` est et que le composant se trouve dans le dossier *pages* :</span><span class="sxs-lookup"><span data-stu-id="13feb-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="13feb-135">L' `Counter` espace de noms du `BlazorApp.Pages`composant est.</span><span class="sxs-lookup"><span data-stu-id="13feb-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="13feb-136">Le nom de type qualifié complet du composant est `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="13feb-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="13feb-137">Pour plus d’informations, consultez la section [importer des composants](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="13feb-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="13feb-138">Pour utiliser un dossier personnalisé, ajoutez l’espace de noms du dossier personnalisé au composant parent ou au fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="13feb-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="13feb-139">Par exemple, l’espace de noms suivant rend les composants dans un dossier de *composants* disponibles lorsque l’espace `BlazorApp`de noms racine de l’application est :</span><span class="sxs-lookup"><span data-stu-id="13feb-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="13feb-140">Les ressources statiques</span><span class="sxs-lookup"><span data-stu-id="13feb-140">Static assets</span></span>

Blazor<span data-ttu-id="13feb-141">suit la Convention de ASP.NET Core les applications qui placent des ressources statiques dans le [dossier racine Web (Wwwroot)](xref:fundamentals/index#web-root)du projet.</span><span class="sxs-lookup"><span data-stu-id="13feb-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="13feb-142">Utilisez un chemin d’accès relatif à`/`la base () pour faire référence à la racine Web d’une ressource statique.</span><span class="sxs-lookup"><span data-stu-id="13feb-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="13feb-143">Dans l’exemple suivant, *logo. png* se trouve physiquement dans le dossier *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="13feb-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="13feb-144">Les composants Razor ne prennent **pas** en charge la notation`~/`tilde-slash ().</span><span class="sxs-lookup"><span data-stu-id="13feb-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="13feb-145">Pour plus d’informations sur la définition du chemin d’accès <xref:host-and-deploy/blazor/index#app-base-path>de base d’une application, consultez.</span><span class="sxs-lookup"><span data-stu-id="13feb-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="13feb-146">Les balises tag Helper ne sont pas prises en charge dans les composants</span><span class="sxs-lookup"><span data-stu-id="13feb-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="13feb-147">Les [tag helpers](xref:mvc/views/tag-helpers/intro) ne sont pas pris en charge dans les composants Razor (fichiers *. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="13feb-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="13feb-148">Pour fournir des fonctionnalités semblables à tag Helper dans Blazor, créez un composant avec les mêmes fonctionnalités que le tag Helper et utilisez le composant à la place.</span><span class="sxs-lookup"><span data-stu-id="13feb-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="13feb-149">Utiliser des composants</span><span class="sxs-lookup"><span data-stu-id="13feb-149">Use components</span></span>

<span data-ttu-id="13feb-150">Les composants peuvent inclure d’autres composants en les déclarant à l’aide de la syntaxe d’élément HTML.</span><span class="sxs-lookup"><span data-stu-id="13feb-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="13feb-151">Le balisage pour l’utilisation d’un composant ressemble à une balise HTML où le nom de la balise est le type du composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="13feb-152">Le balisage suivant dans *index. Razor* rend une `HeadingComponent` instance de :</span><span class="sxs-lookup"><span data-stu-id="13feb-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="13feb-153">*Composants/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="13feb-154">Si un composant contient un élément HTML avec une première lettre majuscule qui ne correspond pas à un nom de composant, un avertissement est émis pour indiquer que l’élément a un nom inattendu.</span><span class="sxs-lookup"><span data-stu-id="13feb-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="13feb-155">L’ajout `@using` d’une directive pour l’espace de noms du composant rend le composant disponible, ce qui résout l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="13feb-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="13feb-156">Routage</span><span class="sxs-lookup"><span data-stu-id="13feb-156">Routing</span></span>

<span data-ttu-id="13feb-157">Le routage Blazor dans est effectué en fournissant un modèle de routage à chaque composant accessible dans l’application.</span><span class="sxs-lookup"><span data-stu-id="13feb-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="13feb-158">Lorsqu’un fichier Razor avec une `@page` directive est compilé, la classe générée reçoit un qui <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> spécifie le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="13feb-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="13feb-159">Lors de l’exécution, le routeur recherche les classes de `RouteAttribute` composant avec un et rend le composant qui a un modèle de routage correspondant à l’URL demandée.</span><span class="sxs-lookup"><span data-stu-id="13feb-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="13feb-160">Pour plus d’informations, consultez <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="13feb-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="13feb-161">Paramètres</span><span class="sxs-lookup"><span data-stu-id="13feb-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="13feb-162">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="13feb-162">Route parameters</span></span>

<span data-ttu-id="13feb-163">Les composants peuvent recevoir des paramètres de routage du modèle de routage `@page` fourni dans la directive.</span><span class="sxs-lookup"><span data-stu-id="13feb-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="13feb-164">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants.</span><span class="sxs-lookup"><span data-stu-id="13feb-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="13feb-165">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="13feb-166">Les paramètres facultatifs ne sont pas `@page` pris en charge. deux directives sont donc appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="13feb-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="13feb-167">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="13feb-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="13feb-168">La deuxième `@page` directive reçoit le `{text}` paramètre d’itinéraire et assigne la valeur à `Text` la propriété.</span><span class="sxs-lookup"><span data-stu-id="13feb-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="13feb-169">La syntaxe de paramètre *catch-all* (`*`/`**`), qui capture le chemin d’accès dans plusieurs limites de dossiers, n’est **pas** prise en charge dans les composants Razor (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="13feb-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="13feb-170">Paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="13feb-170">Component parameters</span></span>

<span data-ttu-id="13feb-171">Les composants peuvent avoir des *paramètres de composant*, qui sont définis à l’aide de propriétés publiques `[Parameter]` sur la classe de composant avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="13feb-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="13feb-172">Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.</span><span class="sxs-lookup"><span data-stu-id="13feb-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="13feb-173">*Composants/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="13feb-174">Dans l’exemple suivant tiré de l’exemple d’application `ParentComponent` , le définit la valeur `Title` de la propriété `ChildComponent`de.</span><span class="sxs-lookup"><span data-stu-id="13feb-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="13feb-175">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="13feb-176">Ne créez pas de composants qui écrivent dans leurs propres *paramètres de composant*, utilisez un champ privé à la place.</span><span class="sxs-lookup"><span data-stu-id="13feb-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="13feb-177">Pour plus d’informations, consultez la section [ne pas créer de composants qui écrivent dans leur propre propriété de paramètre](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="13feb-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="13feb-178">Contenu enfant</span><span class="sxs-lookup"><span data-stu-id="13feb-178">Child content</span></span>

<span data-ttu-id="13feb-179">Les composants peuvent définir le contenu d’un autre composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-179">Components can set the content of another component.</span></span> <span data-ttu-id="13feb-180">Le composant d’affectation fournit le contenu entre les balises qui spécifient le composant récepteur.</span><span class="sxs-lookup"><span data-stu-id="13feb-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="13feb-181">Dans l’exemple suivant, `ChildComponent` a une `ChildContent` propriété qui représente un `RenderFragment`, qui représente un segment de l’interface utilisateur à restituer.</span><span class="sxs-lookup"><span data-stu-id="13feb-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="13feb-182">La valeur de `ChildContent` est positionnée dans le balisage du composant où le contenu doit être rendu.</span><span class="sxs-lookup"><span data-stu-id="13feb-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="13feb-183">La valeur de `ChildContent` est reçue du composant parent et rendue à l’intérieur du panneau de `panel-body`démarrage.</span><span class="sxs-lookup"><span data-stu-id="13feb-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="13feb-184">*Composants/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="13feb-185">La propriété qui reçoit `RenderFragment` le contenu doit être `ChildContent` nommée par Convention.</span><span class="sxs-lookup"><span data-stu-id="13feb-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="13feb-186">`ParentComponent` Dans l’exemple d’application, vous pouvez fournir du contenu `ChildComponent` pour le rendu de en plaçant `<ChildComponent>` le contenu à l’intérieur des balises.</span><span class="sxs-lookup"><span data-stu-id="13feb-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="13feb-187">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="13feb-188">Réprojection d’attribut et paramètres arbitraires</span><span class="sxs-lookup"><span data-stu-id="13feb-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="13feb-189">Les composants peuvent capturer et restituer des attributs supplémentaires en plus des paramètres déclarés du composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="13feb-190">Des attributs supplémentaires peuvent être capturés dans un dictionnaire *, puis* réintégrés à un élément lorsque le composant est rendu [`@attributes`](xref:mvc/views/razor#attributes) à l’aide de la directive Razor.</span><span class="sxs-lookup"><span data-stu-id="13feb-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="13feb-191">Ce scénario est utile lors de la définition d’un composant qui produit un élément de balisage qui prend en charge diverses personnalisations.</span><span class="sxs-lookup"><span data-stu-id="13feb-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="13feb-192">Par exemple, il peut être fastidieux de définir des attributs séparément pour `<input>` un qui prend en charge de nombreux paramètres.</span><span class="sxs-lookup"><span data-stu-id="13feb-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="13feb-193">Dans l’exemple suivant, le premier `<input>` élément (`id="useIndividualParams"`) utilise des paramètres de composant individuels, tandis que le deuxième `<input>` élément (`id="useAttributesDict"`) utilise la projection d’attributs :</span><span class="sxs-lookup"><span data-stu-id="13feb-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="13feb-194">Le type du paramètre doit implémenter `IEnumerable<KeyValuePair<string, object>>` avec des clés de chaîne.</span><span class="sxs-lookup"><span data-stu-id="13feb-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="13feb-195">L' `IReadOnlyDictionary<string, object>` utilisation de est également une option dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="13feb-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="13feb-196">Les éléments `<input>` rendus à l’aide des deux approches sont identiques :</span><span class="sxs-lookup"><span data-stu-id="13feb-196">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="13feb-197">Pour accepter des attributs arbitraires, définissez un paramètre de `[Parameter]` composant à l' `CaptureUnmatchedValues` aide de l' `true`attribut avec la propriété définie sur :</span><span class="sxs-lookup"><span data-stu-id="13feb-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="13feb-198">La `CaptureUnmatchedValues` propriété sur `[Parameter]` permet au paramètre de correspondre à tous les attributs qui ne correspondent à aucun autre paramètre.</span><span class="sxs-lookup"><span data-stu-id="13feb-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="13feb-199">Un composant ne peut définir qu’un seul paramètre `CaptureUnmatchedValues`avec.</span><span class="sxs-lookup"><span data-stu-id="13feb-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="13feb-200">Le type de propriété utilisé `CaptureUnmatchedValues` avec doit pouvoir être assigné `Dictionary<string, object>` à partir de avec des clés de chaîne.</span><span class="sxs-lookup"><span data-stu-id="13feb-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="13feb-201">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` sont également des options dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="13feb-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="13feb-202">La position `@attributes` relative à la position des attributs d’élément est importante.</span><span class="sxs-lookup"><span data-stu-id="13feb-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="13feb-203">Quand `@attributes` sont représentées sur l’élément, les attributs sont traités de droite à gauche (dernier à premier).</span><span class="sxs-lookup"><span data-stu-id="13feb-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="13feb-204">Prenons l’exemple suivant d’un composant qui consomme un `Child` composant :</span><span class="sxs-lookup"><span data-stu-id="13feb-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="13feb-205">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="13feb-206">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="13feb-207">L' `Child` attribut du `extra` composant est défini à droite de `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="13feb-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="13feb-208">Le `Parent` rendu `<div>` du composant contient `extra="5"` lorsqu’il est transmis via l’attribut supplémentaire, car les attributs sont traités de droite à gauche (dernier à premier) :</span><span class="sxs-lookup"><span data-stu-id="13feb-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="13feb-209">Dans l’exemple suivant, l’ordre de `extra` et `@attributes` est inversé dans le `Child` d’un `<div>`composant :</span><span class="sxs-lookup"><span data-stu-id="13feb-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="13feb-210">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="13feb-211">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="13feb-212">Le rendu `<div>` dans le `Parent` composant contient `extra="10"` lorsqu’il est passé par le biais de l’attribut supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="13feb-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="13feb-213">Capturer des références à des composants</span><span class="sxs-lookup"><span data-stu-id="13feb-213">Capture references to components</span></span>

<span data-ttu-id="13feb-214">Les références de composant offrent un moyen de référencer une instance de composant afin que vous puissiez émettre des commandes vers `Show` cette `Reset`instance, telles que ou.</span><span class="sxs-lookup"><span data-stu-id="13feb-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="13feb-215">Pour capturer une référence de composant :</span><span class="sxs-lookup"><span data-stu-id="13feb-215">To capture a component reference:</span></span>

* <span data-ttu-id="13feb-216">Ajoutez un [`@ref`](xref:mvc/views/razor#ref) attribut au composant enfant.</span><span class="sxs-lookup"><span data-stu-id="13feb-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="13feb-217">Définissez un champ avec le même type que le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="13feb-217">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="13feb-218">Lors du rendu du composant, le `_loginDialog` champ est rempli avec l' `MyLoginDialog` instance du composant enfant.</span><span class="sxs-lookup"><span data-stu-id="13feb-218">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="13feb-219">Vous pouvez ensuite appeler des méthodes .NET sur l’instance du composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13feb-220">La `_loginDialog` variable est remplie uniquement après le rendu du composant et sa sortie comprend l' `MyLoginDialog` élément.</span><span class="sxs-lookup"><span data-stu-id="13feb-220">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="13feb-221">Jusqu’à ce stade, il n’y a rien à référencer.</span><span class="sxs-lookup"><span data-stu-id="13feb-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="13feb-222">Pour manipuler des références de composants après la fin du rendu du composant, utilisez les [méthodes OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="13feb-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="13feb-223">Pour référencer des composants dans une boucle, consultez [capturer des références à plusieurs composants enfants similaires (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="13feb-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="13feb-224">Bien que la capture de références de composant utilise une syntaxe similaire pour [capturer des références d’élément](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), il ne s’agit pas d’une fonctionnalité JavaScript Interop.</span><span class="sxs-lookup"><span data-stu-id="13feb-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="13feb-225">Les références de composant ne sont pas&mdash;transmises au code JavaScript et ne sont utilisées que dans le code .net.</span><span class="sxs-lookup"><span data-stu-id="13feb-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="13feb-226">N’utilisez **pas** de références de composant pour muter l’état des composants enfants.</span><span class="sxs-lookup"><span data-stu-id="13feb-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="13feb-227">Utilisez plutôt des paramètres déclaratifs normaux pour passer des données aux composants enfants.</span><span class="sxs-lookup"><span data-stu-id="13feb-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="13feb-228">L’utilisation de paramètres déclaratifs normaux entraîne le rerendu automatique des composants enfants.</span><span class="sxs-lookup"><span data-stu-id="13feb-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="13feb-229">Appeler des méthodes de composant en externe pour mettre à jour l’État</span><span class="sxs-lookup"><span data-stu-id="13feb-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="13feb-230">utilise un `SynchronizationContext` pour appliquer un seul thread logique d’exécution.</span><span class="sxs-lookup"><span data-stu-id="13feb-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="13feb-231">Les méthodes de [cycle de vie](xref:blazor/lifecycle) d’un composant et les rappels d' Blazor événements déclenchés par `SynchronizationContext`sont exécutés sur ce.</span><span class="sxs-lookup"><span data-stu-id="13feb-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="13feb-232">Dans le cas où un composant doit être mis à jour en fonction d’un événement externe, tel qu’un minuteur ou `InvokeAsync` d’autres notifications, utilisez Blazorla `SynchronizationContext`méthode, qui sera réexpédiée à.</span><span class="sxs-lookup"><span data-stu-id="13feb-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="13feb-233">Par exemple, considérez un *service de notification* qui peut notifier n’importe quel composant d’écoute de l’État mis à jour :</span><span class="sxs-lookup"><span data-stu-id="13feb-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="13feb-234">Inscrivez le `NotifierService` en tant que singletion :</span><span class="sxs-lookup"><span data-stu-id="13feb-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="13feb-235">Dans Blazor webassembly, inscrivez le service `Program.Main`dans :</span><span class="sxs-lookup"><span data-stu-id="13feb-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="13feb-236">Dans Blazor serveur, inscrivez le service dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="13feb-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="13feb-237">Utilisez `NotifierService` pour mettre à jour un composant :</span><span class="sxs-lookup"><span data-stu-id="13feb-237">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="13feb-238">Dans l’exemple précédent, `NotifierService` appelle la méthode du `OnNotify` composant en dehors de Blazor `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="13feb-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="13feb-239">`InvokeAsync`est utilisé pour basculer vers le contexte correct et pour la mise en file d’attente d’un rendu.</span><span class="sxs-lookup"><span data-stu-id="13feb-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="13feb-240">Utiliser \@la clé pour contrôler la conservation des éléments et des composants</span><span class="sxs-lookup"><span data-stu-id="13feb-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="13feb-241">Lors du rendu d’une liste d’éléments ou de composants, et si les éléments Blazorou les composants changent par la suite, l’algorithme de différenciation de doit décider quels éléments ou composants précédents peuvent être conservés et comment les objets de modèle doivent être mappés à eux.</span><span class="sxs-lookup"><span data-stu-id="13feb-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="13feb-242">Normalement, ce processus est automatique et peut être ignoré, mais dans certains cas, il peut être utile de contrôler le processus.</span><span class="sxs-lookup"><span data-stu-id="13feb-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="13feb-243">Prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="13feb-243">Consider the following example:</span></span>

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

<span data-ttu-id="13feb-244">Le contenu de la `People` collection peut changer avec des entrées insérées, supprimées ou réorganisées.</span><span class="sxs-lookup"><span data-stu-id="13feb-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="13feb-245">Lors du rerendu du composant, le `<DetailsEditor>` composant peut changer pour recevoir des `Details` valeurs de paramètre différentes.</span><span class="sxs-lookup"><span data-stu-id="13feb-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="13feb-246">Cela peut entraîner un rerendu plus complexe que prévu.</span><span class="sxs-lookup"><span data-stu-id="13feb-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="13feb-247">Dans certains cas, le rerendu peut entraîner des différences de comportement visibles, telles que le focus de l’élément perdu.</span><span class="sxs-lookup"><span data-stu-id="13feb-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="13feb-248">Le processus de mappage peut être contrôlé à [`@key`](xref:mvc/views/razor#key) l’aide de l’attribut directive.</span><span class="sxs-lookup"><span data-stu-id="13feb-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="13feb-249">`@key`force l’algorithme de comparaison à garantir la préservation des éléments ou des composants en fonction de la valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="13feb-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="13feb-250">Lorsque la `People` collection est modifiée, l’algorithme de comparaison conserve l’Association `<DetailsEditor>` entre les `person` instances et les instances :</span><span class="sxs-lookup"><span data-stu-id="13feb-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="13feb-251">Si un `Person` est supprimé de la `People` liste, seule l’instance `<DetailsEditor>` correspondante est supprimée de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="13feb-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="13feb-252">Les autres instances restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="13feb-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="13feb-253">Si un `Person` est inséré à une position dans la liste, une nouvelle `<DetailsEditor>` instance est insérée à cette position correspondante.</span><span class="sxs-lookup"><span data-stu-id="13feb-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="13feb-254">Les autres instances restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="13feb-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="13feb-255">Si `Person` les entrées sont réordonnées, les `<DetailsEditor>` instances correspondantes sont conservées et réordonnées dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="13feb-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="13feb-256">Dans certains scénarios, l’utilisation `@key` de réduit la complexité du rerendu et évite les problèmes potentiels liés aux parties avec état de la modification du modèle DOM, telles que la position du focus.</span><span class="sxs-lookup"><span data-stu-id="13feb-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13feb-257">Les clés sont locales pour chaque élément ou composant conteneur.</span><span class="sxs-lookup"><span data-stu-id="13feb-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="13feb-258">Les clés ne sont pas comparées globalement dans le document.</span><span class="sxs-lookup"><span data-stu-id="13feb-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="13feb-259">Quand utiliser \@la clé</span><span class="sxs-lookup"><span data-stu-id="13feb-259">When to use \@key</span></span>

<span data-ttu-id="13feb-260">En règle générale, il est judicieux `@key` d’utiliser chaque fois qu’une liste est rendue (par `@foreach` exemple, dans un bloc) et qu’une valeur `@key`appropriée existe pour définir.</span><span class="sxs-lookup"><span data-stu-id="13feb-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="13feb-261">Vous pouvez également utiliser `@key` pour empêcher Blazor de conserver un élément ou une sous-arborescence de composants lorsqu’un objet change :</span><span class="sxs-lookup"><span data-stu-id="13feb-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="13feb-262">En `@currentPerson` cas de modification `@key` , la directive Blazor d’attribut force à `<div>` ignorer la totalité et ses descendants, et à reconstruire la sous-arborescence de l’interface utilisateur avec de nouveaux éléments et composants.</span><span class="sxs-lookup"><span data-stu-id="13feb-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="13feb-263">Cela peut être utile si vous devez garantir qu’aucun État d’interface utilisateur n’est `@currentPerson` préservé lorsque des modifications sont apportées.</span><span class="sxs-lookup"><span data-stu-id="13feb-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="13feb-264">Quand ne pas utiliser \@la clé</span><span class="sxs-lookup"><span data-stu-id="13feb-264">When not to use \@key</span></span>

<span data-ttu-id="13feb-265">Il y a un coût en matière de performances `@key`lors de la comparaison avec.</span><span class="sxs-lookup"><span data-stu-id="13feb-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="13feb-266">Le coût des performances n’est pas important, `@key` mais spécifiez uniquement si les règles de conservation des éléments ou des composants bénéficient de l’application.</span><span class="sxs-lookup"><span data-stu-id="13feb-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="13feb-267">Même si `@key` n’est pas Blazor utilisé, conserve autant que possible les instances d’élément et de composant enfants.</span><span class="sxs-lookup"><span data-stu-id="13feb-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="13feb-268">Le seul avantage de l' `@key` utilisation de est de contrôler la *façon dont* les instances de modèle sont mappées aux instances de composant conservées, au lieu de l’algorithme de comparaison qui sélectionne le mappage.</span><span class="sxs-lookup"><span data-stu-id="13feb-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="13feb-269">Valeurs à utiliser pour \@la clé</span><span class="sxs-lookup"><span data-stu-id="13feb-269">What values to use for \@key</span></span>

<span data-ttu-id="13feb-270">En règle générale, il est logique de fournir l’un des types de valeur `@key`suivants pour :</span><span class="sxs-lookup"><span data-stu-id="13feb-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="13feb-271">Instances d’objet de modèle (par exemple `Person` , une instance comme dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="13feb-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="13feb-272">Cela garantit la préservation en fonction de l’égalité des références d’objet.</span><span class="sxs-lookup"><span data-stu-id="13feb-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="13feb-273">Identificateurs uniques (par exemple, les valeurs de clé primaire `int`de `string`type, `Guid`ou).</span><span class="sxs-lookup"><span data-stu-id="13feb-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="13feb-274">Vérifiez que les valeurs utilisées `@key` pour ne sont pas en conflit.</span><span class="sxs-lookup"><span data-stu-id="13feb-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="13feb-275">Si les valeurs en conflit sont détectées dans le même élément Blazor parent, lève une exception, car il ne peut pas mapper de manière déterministe les anciens éléments ou composants aux nouveaux éléments ou composants.</span><span class="sxs-lookup"><span data-stu-id="13feb-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="13feb-276">Utilisez uniquement des valeurs distinctes, telles que des instances d’objets ou des valeurs de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="13feb-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="13feb-277">Ne créez pas de composants qui écrivent dans leurs propres propriétés de paramètre</span><span class="sxs-lookup"><span data-stu-id="13feb-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="13feb-278">Les paramètres sont remplacés dans les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="13feb-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="13feb-279">Le contenu d’un composant enfant est rendu avec `RenderFragment`un.</span><span class="sxs-lookup"><span data-stu-id="13feb-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="13feb-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé dans le composant parent.</span><span class="sxs-lookup"><span data-stu-id="13feb-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="13feb-281">Les paramètres sont réinitialisés, car le composant parent <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est restitué à nouveau lorsque est appelé et de nouvelles valeurs de paramètres sont fournies au composant enfant.</span><span class="sxs-lookup"><span data-stu-id="13feb-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="13feb-282">Prenons le composant `Expander` suivant :</span><span class="sxs-lookup"><span data-stu-id="13feb-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="13feb-283">Restitue le contenu enfant.</span><span class="sxs-lookup"><span data-stu-id="13feb-283">Renders child content.</span></span>
* <span data-ttu-id="13feb-284">Active ou désactive l’indication du contenu enfant avec un paramètre de composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-284">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="13feb-285">Le `Expander` composant est ajouté à un composant parent qui peut appeler `StateHasChanged`:</span><span class="sxs-lookup"><span data-stu-id="13feb-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="13feb-286">Au départ, `Expander` les composants se comportent indépendamment lorsque leurs `Expanded` propriétés sont basculées.</span><span class="sxs-lookup"><span data-stu-id="13feb-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="13feb-287">Les composants enfants maintiennent leurs États comme prévu.</span><span class="sxs-lookup"><span data-stu-id="13feb-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="13feb-288">Lorsque `StateHasChanged` est appelé dans le parent, le `Expanded` paramètre du premier composant enfant est réinitialisé à sa valeur initiale (`true`).</span><span class="sxs-lookup"><span data-stu-id="13feb-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="13feb-289">La valeur `Expander` du `Expanded` deuxième composant n’est pas réinitialisée, car aucun contenu enfant n’est restitué dans le deuxième composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="13feb-290">Pour maintenir l’État dans le scénario précédent, utilisez un *champ privé* dans `Expander` le composant pour maintenir son état bascule.</span><span class="sxs-lookup"><span data-stu-id="13feb-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="13feb-291">Le composant `Expander` suivant :</span><span class="sxs-lookup"><span data-stu-id="13feb-291">The following `Expander` component:</span></span>

* <span data-ttu-id="13feb-292">Accepte la `Expanded` valeur de paramètre du composant à partir du parent.</span><span class="sxs-lookup"><span data-stu-id="13feb-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="13feb-293">Affecte la valeur du paramètre de composant à un *champ privé* (`_expanded`) dans l' [événement OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="13feb-293">Assigns the component parameter value to a *private field* (`_expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="13feb-294">Utilise le champ privé pour conserver son état bascule interne.</span><span class="sxs-lookup"><span data-stu-id="13feb-294">Uses the private field to maintain its internal toggle state.</span></span>

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

## <a name="partial-class-support"></a><span data-ttu-id="13feb-295">Prise en charge des classes partielles</span><span class="sxs-lookup"><span data-stu-id="13feb-295">Partial class support</span></span>

<span data-ttu-id="13feb-296">Les composants Razor sont générés en tant que classes partielles.</span><span class="sxs-lookup"><span data-stu-id="13feb-296">Razor components are generated as partial classes.</span></span> <span data-ttu-id="13feb-297">Les composants Razor sont créés à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="13feb-297">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="13feb-298">Le code C# est défini dans [`@code`](xref:mvc/views/razor#code) un bloc avec le balisage HTML et le code Razor dans un fichier unique.</span><span class="sxs-lookup"><span data-stu-id="13feb-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="13feb-299">les modèles définissent leurs composants Razor à l’aide de cette approche.</span><span class="sxs-lookup"><span data-stu-id="13feb-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="13feb-300">Le code C# est placé dans un fichier code-behind défini en tant que classe partielle.</span><span class="sxs-lookup"><span data-stu-id="13feb-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="13feb-301">L’exemple suivant montre le composant `Counter` par défaut avec `@code` un bloc dans une application générée à Blazor partir d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="13feb-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="13feb-302">Le balisage HTML, le code Razor et le code C# se trouvent dans le même fichier :</span><span class="sxs-lookup"><span data-stu-id="13feb-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="13feb-303">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-303">*Counter.razor*:</span></span>

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

<span data-ttu-id="13feb-304">Le `Counter` composant peut également être créé à l’aide d’un fichier code-behind avec une classe partielle :</span><span class="sxs-lookup"><span data-stu-id="13feb-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="13feb-305">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="13feb-306">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="13feb-306">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="13feb-307">Ajoutez tous les espaces de noms requis au fichier de classe partielle, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="13feb-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="13feb-308">Les espaces de noms standard utilisés par les composants Razor sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="13feb-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="13feb-309">Spécifier une classe de base</span><span class="sxs-lookup"><span data-stu-id="13feb-309">Specify a base class</span></span>

<span data-ttu-id="13feb-310">La [`@inherits`](xref:mvc/views/razor#inherits) directive peut être utilisée pour spécifier une classe de base pour un composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="13feb-311">L’exemple suivant montre comment un composant peut hériter d’une classe `BlazorRocksBase`de base,, pour fournir les propriétés et les méthodes du composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="13feb-312">La classe de base doit dériver de `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="13feb-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="13feb-313">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="13feb-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="13feb-314">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="13feb-314">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="13feb-315">Spécifier un attribut</span><span class="sxs-lookup"><span data-stu-id="13feb-315">Specify an attribute</span></span>

<span data-ttu-id="13feb-316">Les attributs peuvent être spécifiés dans les composants [`@attribute`](xref:mvc/views/razor#attribute) Razor avec la directive.</span><span class="sxs-lookup"><span data-stu-id="13feb-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="13feb-317">L’exemple suivant applique l' `[Authorize]` attribut à la classe de composant :</span><span class="sxs-lookup"><span data-stu-id="13feb-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="13feb-318">Importer des composants</span><span class="sxs-lookup"><span data-stu-id="13feb-318">Import components</span></span>

<span data-ttu-id="13feb-319">L’espace de noms d’un composant créé avec Razor est basé sur (par ordre de priorité) :</span><span class="sxs-lookup"><span data-stu-id="13feb-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="13feb-320">[`@namespace`](xref:mvc/views/razor#namespace)désignation dans le balisage du fichier Razor (*. Razor*) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="13feb-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="13feb-321">Du projet `RootNamespace` dans le fichier projet (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="13feb-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="13feb-322">Nom du projet, pris à partir du nom de fichier du fichier projet (*. csproj*), et chemin d’accès de la racine du projet au composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="13feb-323">Par exemple, le Framework résout *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) en espace de noms `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="13feb-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="13feb-324">Les composants suivent les règles de liaison de noms C#.</span><span class="sxs-lookup"><span data-stu-id="13feb-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="13feb-325">Pour le `Index` composant dans cet exemple, les composants de l’étendue sont tous les composants :</span><span class="sxs-lookup"><span data-stu-id="13feb-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="13feb-326">Dans le même dossier, *pages*.</span><span class="sxs-lookup"><span data-stu-id="13feb-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="13feb-327">Composants de la racine du projet qui ne spécifient pas explicitement un espace de noms différent.</span><span class="sxs-lookup"><span data-stu-id="13feb-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="13feb-328">Les composants définis dans un espace de noms différent sont mis en portée [`@using`](xref:mvc/views/razor#using) à l’aide de la directive Razor.</span><span class="sxs-lookup"><span data-stu-id="13feb-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="13feb-329">Si un autre composant `NavMenu.razor`,, existe dans le dossier *BlazorSample/Shared/* , le composant peut être `Index.razor` utilisé dans avec `@using` l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="13feb-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="13feb-330">Les composants peuvent également être référencés à l’aide de leurs noms complets, ce [`@using`](xref:mvc/views/razor#using) qui ne requiert pas la directive :</span><span class="sxs-lookup"><span data-stu-id="13feb-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="13feb-331">La `global::` qualification n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="13feb-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="13feb-332">L’importation de composants avec `using` des instructions avec alias ( `@using Foo = Bar`par exemple,) n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="13feb-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="13feb-333">Les noms partiellement qualifiés ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="13feb-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="13feb-334">Par exemple, l' `@using BlazorSample` ajout et `NavMenu.razor` la `<Shared.NavMenu></Shared.NavMenu>` référencement avec ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="13feb-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="13feb-335">Attributs d’éléments HTML conditionnels</span><span class="sxs-lookup"><span data-stu-id="13feb-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="13feb-336">Les attributs des éléments HTML sont rendus de manière conditionnelle en fonction de la valeur .NET.</span><span class="sxs-lookup"><span data-stu-id="13feb-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="13feb-337">Si la valeur est `false` ou `null`, l’attribut n’est pas rendu.</span><span class="sxs-lookup"><span data-stu-id="13feb-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="13feb-338">Si la valeur est `true`, l’attribut est rendu réduit.</span><span class="sxs-lookup"><span data-stu-id="13feb-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="13feb-339">Dans l’exemple suivant, `IsCompleted` détermine si `checked` est rendu dans le balisage de l’élément :</span><span class="sxs-lookup"><span data-stu-id="13feb-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="13feb-340">Si `IsCompleted` est `true`, la case à cocher s’affiche comme suit :</span><span class="sxs-lookup"><span data-stu-id="13feb-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="13feb-341">Si `IsCompleted` est `false`, la case à cocher s’affiche comme suit :</span><span class="sxs-lookup"><span data-stu-id="13feb-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="13feb-342">Pour plus d’informations, consultez <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="13feb-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="13feb-343">Certains attributs HTML, tels que [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), ne fonctionnent pas correctement quand le type .net est `bool`.</span><span class="sxs-lookup"><span data-stu-id="13feb-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="13feb-344">Dans ce cas, utilisez un `string` type au lieu d' `bool`un.</span><span class="sxs-lookup"><span data-stu-id="13feb-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="13feb-345">HTML brut</span><span class="sxs-lookup"><span data-stu-id="13feb-345">Raw HTML</span></span>

<span data-ttu-id="13feb-346">Les chaînes sont normalement rendues à l’aide de nœuds de texte DOM, ce qui signifie que tout balisage qu’elles peuvent contenir est ignorée et traitée comme du texte littéral.</span><span class="sxs-lookup"><span data-stu-id="13feb-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="13feb-347">Pour afficher le code HTML brut, encapsulez le `MarkupString` contenu HTML dans une valeur.</span><span class="sxs-lookup"><span data-stu-id="13feb-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="13feb-348">La valeur est analysée au format HTML ou SVG et est insérée dans le DOM.</span><span class="sxs-lookup"><span data-stu-id="13feb-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="13feb-349">Le rendu de code HTML brut construit à partir de n’importe quelle source non approuvée constitue un risque pour la **sécurité** et doit être évité !</span><span class="sxs-lookup"><span data-stu-id="13feb-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="13feb-350">L’exemple suivant illustre l’utilisation `MarkupString` du type pour ajouter un bloc de contenu HTML statique à la sortie rendue d’un composant :</span><span class="sxs-lookup"><span data-stu-id="13feb-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="13feb-351">Valeurs et paramètres en cascade</span><span class="sxs-lookup"><span data-stu-id="13feb-351">Cascading values and parameters</span></span>

<span data-ttu-id="13feb-352">Dans certains scénarios, il est peu commode de transmettre des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composant](#component-parameters), en particulier lorsqu’il existe plusieurs couches de composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="13feb-353">Les valeurs et paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant ancêtre de fournir une valeur à tous ses composants descendants.</span><span class="sxs-lookup"><span data-stu-id="13feb-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="13feb-354">Les valeurs et les paramètres en cascade fournissent également une approche pour les composants à coordonner.</span><span class="sxs-lookup"><span data-stu-id="13feb-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="13feb-355">Exemple de thème</span><span class="sxs-lookup"><span data-stu-id="13feb-355">Theme example</span></span>

<span data-ttu-id="13feb-356">Dans l’exemple suivant tiré de l’exemple d’application `ThemeInfo` , la classe spécifie les informations de thème pour descendre dans la hiérarchie des composants, de sorte que tous les boutons d’une partie donnée de l’application partagent le même style.</span><span class="sxs-lookup"><span data-stu-id="13feb-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="13feb-357">*UIThemeClasses/themeinfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="13feb-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="13feb-358">Un composant ancêtre peut fournir une valeur en cascade à l’aide du composant de valeur en cascade.</span><span class="sxs-lookup"><span data-stu-id="13feb-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="13feb-359">Le `CascadingValue` composant encapsule une sous-arborescence de la hiérarchie des composants et fournit une valeur unique à tous les composants de cette sous-arborescence.</span><span class="sxs-lookup"><span data-stu-id="13feb-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="13feb-360">Par exemple, l’exemple d’application spécifie les`ThemeInfo`informations de thème () dans l’une des dispositions de l’application en tant que paramètre en cascade pour tous les composants qui composent le corps de la disposition de la `@Body` propriété.</span><span class="sxs-lookup"><span data-stu-id="13feb-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="13feb-361">`ButtonClass`la valeur est affectée `btn-success` à dans le composant Layout.</span><span class="sxs-lookup"><span data-stu-id="13feb-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="13feb-362">Tout composant descendant peut consommer cette propriété par le `ThemeInfo` biais de l’objet en cascade.</span><span class="sxs-lookup"><span data-stu-id="13feb-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="13feb-363">`CascadingValuesParametersLayout`-</span><span class="sxs-lookup"><span data-stu-id="13feb-363">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="13feb-364">Pour utiliser des valeurs en cascade, les composants déclarent des paramètres en cascade `[CascadingParameter]` à l’aide de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="13feb-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="13feb-365">Les valeurs en cascade sont liées aux paramètres en cascade par type.</span><span class="sxs-lookup"><span data-stu-id="13feb-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="13feb-366">Dans l’exemple d’application, `CascadingValuesParametersTheme` le composant lie la `ThemeInfo` valeur en cascade à un paramètre en cascade.</span><span class="sxs-lookup"><span data-stu-id="13feb-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="13feb-367">Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="13feb-368">`CascadingValuesParametersTheme`-</span><span class="sxs-lookup"><span data-stu-id="13feb-368">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="13feb-369">Pour mettre en cascade plusieurs valeurs du même type dans la même sous-arborescence, fournissez une `Name` chaîne `CascadingValue` unique à chaque composant `CascadingParameter`et à son correspondant.</span><span class="sxs-lookup"><span data-stu-id="13feb-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="13feb-370">Dans l’exemple suivant, deux `CascadingValue` composants montent en cascade `MyCascadingType` différentes instances de par nom :</span><span class="sxs-lookup"><span data-stu-id="13feb-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="13feb-371">Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans le composant ancêtre par nom :</span><span class="sxs-lookup"><span data-stu-id="13feb-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="13feb-372">Exemple TabSet</span><span class="sxs-lookup"><span data-stu-id="13feb-372">TabSet example</span></span>

<span data-ttu-id="13feb-373">Les paramètres en cascade permettent également aux composants de collaborer au sein de la hiérarchie des composants.</span><span class="sxs-lookup"><span data-stu-id="13feb-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="13feb-374">Par exemple, considérez l’exemple *TabSet* suivant dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="13feb-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="13feb-375">L’exemple d’application possède `ITab` une interface qui implémente les onglets suivants :</span><span class="sxs-lookup"><span data-stu-id="13feb-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="13feb-376">Le `CascadingValuesParametersTabSet` composant utilise le `TabSet` composant, qui contient plusieurs `Tab` composants :</span><span class="sxs-lookup"><span data-stu-id="13feb-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="13feb-377">Les composants `Tab` enfants ne sont pas explicitement passés comme paramètres `TabSet`à.</span><span class="sxs-lookup"><span data-stu-id="13feb-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="13feb-378">Au lieu de cela `Tab` , les composants enfants font partie du contenu enfant `TabSet`de.</span><span class="sxs-lookup"><span data-stu-id="13feb-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="13feb-379">Toutefois, le `TabSet` doit toujours connaître chaque `Tab` composant pour pouvoir afficher les en-têtes et l’onglet actif. Pour activer cette coordination sans nécessiter de code supplémentaire `TabSet` , le composant *peut se présenter comme une valeur en cascade* qui est ensuite récupérée par les `Tab` composants descendants.</span><span class="sxs-lookup"><span data-stu-id="13feb-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="13feb-380">`TabSet`-</span><span class="sxs-lookup"><span data-stu-id="13feb-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="13feb-381">Les composants `Tab` descendants capturent `TabSet` le contenant comme paramètre en cascade, de `Tab` sorte que les composants s' `TabSet` ajoutent eux-mêmes à la et à la coordonnée sur laquelle l’onglet est actif.</span><span class="sxs-lookup"><span data-stu-id="13feb-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="13feb-382">`Tab`-</span><span class="sxs-lookup"><span data-stu-id="13feb-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="13feb-383">Modèles Razor</span><span class="sxs-lookup"><span data-stu-id="13feb-383">Razor templates</span></span>

<span data-ttu-id="13feb-384">Les fragments de rendu peuvent être définis à l’aide de la syntaxe de modèle Razor.</span><span class="sxs-lookup"><span data-stu-id="13feb-384">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="13feb-385">Les modèles Razor sont un moyen de définir un extrait de code d’interface utilisateur et de supposer le format suivant :</span><span class="sxs-lookup"><span data-stu-id="13feb-385">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="13feb-386">L’exemple suivant montre comment spécifier `RenderFragment` des valeurs et `RenderFragment<T>` et restituer des modèles directement dans un composant.</span><span class="sxs-lookup"><span data-stu-id="13feb-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="13feb-387">Les fragments de rendu peuvent également être passés comme arguments à des [composants basés](xref:blazor/templated-components)sur un modèle.</span><span class="sxs-lookup"><span data-stu-id="13feb-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="13feb-388">Sortie rendue du code précédent :</span><span class="sxs-lookup"><span data-stu-id="13feb-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="13feb-389">Images SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="13feb-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="13feb-390">Étant Blazor donné que le rendu des images HTML, prises en charge par le navigateur, y compris les images SVG (Scalable Vector Graphics) `<img>` (*. svg*), est pris en charge via la balise :</span><span class="sxs-lookup"><span data-stu-id="13feb-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="13feb-391">De même, les images SVG sont prises en charge dans les règles CSS d’un fichier de feuille de style (*. CSS*) :</span><span class="sxs-lookup"><span data-stu-id="13feb-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="13feb-392">Toutefois, le balisage SVG en ligne n’est pas pris en charge dans tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="13feb-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="13feb-393">Si vous placez une `<svg>` balise directement dans un fichier de composant (*. Razor*), le rendu d’image de base est pris en charge, mais de nombreux scénarios avancés ne sont pas encore pris en charge.</span><span class="sxs-lookup"><span data-stu-id="13feb-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="13feb-394">Par exemple, `<use>` les balises ne sont pas `@bind` actuellement respectées et ne peuvent pas être utilisées avec certaines balises SVG.</span><span class="sxs-lookup"><span data-stu-id="13feb-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="13feb-395">Nous prévoyons de traiter ces limitations dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="13feb-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13feb-396">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="13feb-396">Additional resources</span></span>

* <span data-ttu-id="13feb-397"><xref:security/blazor/server/threat-mitigation>&ndash; Contient des conseils sur Blazor la création d’applications serveur qui doivent être en concurrence avec l’épuisement des ressources.</span><span class="sxs-lookup"><span data-stu-id="13feb-397"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
