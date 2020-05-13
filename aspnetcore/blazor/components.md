---
title: Créer et utiliser des Razor composants ASP.net Core
author: guardrex
description: Découvrez comment créer et utiliser des Razor composants, notamment comment lier des données, gérer des événements et gérer des cycles de vie de composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: a7009bf1cf99a15f3617b47a904d52f5787b9ce1
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153516"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="afe9a-103">Créer et utiliser des Razor composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="afe9a-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="afe9a-104">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="afe9a-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="afe9a-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="afe9a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="afe9a-106">les applications sont générées à l’aide de *composants*.</span><span class="sxs-lookup"><span data-stu-id="afe9a-106"> apps are built using *components*.</span></span> <span data-ttu-id="afe9a-107">Un composant est un bloc d’interface utilisateur (IU) autonome, tel qu’une page, une boîte de dialogue ou un formulaire.</span><span class="sxs-lookup"><span data-stu-id="afe9a-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="afe9a-108">Un composant comprend le balisage HTML et la logique de traitement requise pour injecter des données ou répondre à des événements d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="afe9a-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="afe9a-109">Les composants sont flexibles et légers.</span><span class="sxs-lookup"><span data-stu-id="afe9a-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="afe9a-110">Elles peuvent être imbriquées, réutilisées et partagées entre les projets.</span><span class="sxs-lookup"><span data-stu-id="afe9a-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="afe9a-111">Classes de composant</span><span class="sxs-lookup"><span data-stu-id="afe9a-111">Component classes</span></span>

<span data-ttu-id="afe9a-112">Les composants sont implémentés dans les [Razor](xref:mvc/views/razor) fichiers de composants (*. Razor*) à l’aide d’une combinaison de balisage C# et html.</span><span class="sxs-lookup"><span data-stu-id="afe9a-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="afe9a-113">Un composant dans Blazor est officiellement désigné sous le terme de \* Razor composant\*.</span><span class="sxs-lookup"><span data-stu-id="afe9a-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="afe9a-114">Le nom d’un composant doit commencer par un caractère majuscule.</span><span class="sxs-lookup"><span data-stu-id="afe9a-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="afe9a-115">Par exemple, *MyCoolComponent. Razor* est valide et *MyCoolComponent. Razor* n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="afe9a-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="afe9a-116">L’interface utilisateur d’un composant est définie à l’aide de HTML.</span><span class="sxs-lookup"><span data-stu-id="afe9a-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="afe9a-117">La logique de rendu dynamique (par exemple, les boucles, les instructions conditionnelles, les expressions) est ajoutée à l’aide d’une syntaxe C# incorporée appelée [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="afe9a-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="afe9a-118">Quand une application est compilée, le balisage HTML et la logique de rendu C# sont convertis en une classe de composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="afe9a-119">Le nom de la classe générée correspond au nom du fichier.</span><span class="sxs-lookup"><span data-stu-id="afe9a-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="afe9a-120">Les membres de la classe de composants sont définis dans un bloc `@code`.</span><span class="sxs-lookup"><span data-stu-id="afe9a-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="afe9a-121">Dans le `@code` bloc, l’état du composant (propriétés, champs) est spécifié avec des méthodes pour la gestion des événements ou pour la définition d’une autre logique de composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="afe9a-122">Plus d’un bloc `@code` est autorisé.</span><span class="sxs-lookup"><span data-stu-id="afe9a-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="afe9a-123">Les membres de composant peuvent être utilisés dans le cadre de la logique de rendu du composant à l’aide d’expressions C# qui commencent par `@` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="afe9a-124">Par exemple, un champ C# est rendu en préfixant `@` le nom du champ.</span><span class="sxs-lookup"><span data-stu-id="afe9a-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="afe9a-125">L’exemple suivant évalue et affiche :</span><span class="sxs-lookup"><span data-stu-id="afe9a-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="afe9a-126">`headingFontStyle`à la valeur de propriété CSS pour `font-style` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="afe9a-127">`headingText`au contenu de l' `<h1>` élément.</span><span class="sxs-lookup"><span data-stu-id="afe9a-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="afe9a-128">Une fois le composant restitué initialement, le composant régénère son arborescence de rendu en réponse aux événements.</span><span class="sxs-lookup"><span data-stu-id="afe9a-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="afe9a-129">compare ensuite la nouvelle arborescence de rendu à la précédente et applique toutes les modifications apportées au Document Object Model du navigateur (DOM).</span><span class="sxs-lookup"><span data-stu-id="afe9a-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="afe9a-130">Les composants sont des classes C# ordinaires qui peuvent être placées n’importe où dans un projet.</span><span class="sxs-lookup"><span data-stu-id="afe9a-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="afe9a-131">Les composants qui produisent des pages Web résident généralement dans le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="afe9a-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="afe9a-132">Les composants qui ne sont pas des pages sont souvent placés dans le dossier *partagé* ou dans un dossier personnalisé ajouté au projet.</span><span class="sxs-lookup"><span data-stu-id="afe9a-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="afe9a-133">En règle générale, l’espace de noms d’un composant est dérivé de l’espace de noms racine de l’application et de l’emplacement (dossier) du composant dans l’application.</span><span class="sxs-lookup"><span data-stu-id="afe9a-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="afe9a-134">Si l’espace de noms racine de l’application est `BlazorApp` et que le `Counter` composant se trouve dans le dossier *pages* :</span><span class="sxs-lookup"><span data-stu-id="afe9a-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="afe9a-135">L' `Counter` espace de noms du composant est `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="afe9a-136">Le nom de type qualifié complet du composant est `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="afe9a-137">Pour les dossiers personnalisés qui contiennent des composants, ajoutez une `using` instruction au composant parent ou au fichier *_Imports. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="afe9a-137">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="afe9a-138">L’exemple suivant rend les composants du dossier *composants* disponibles :</span><span class="sxs-lookup"><span data-stu-id="afe9a-138">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="afe9a-139">Un composant peut également être référencé directement :</span><span class="sxs-lookup"><span data-stu-id="afe9a-139">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="afe9a-140">Pour plus d’informations, consultez la section [importer des composants](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="afe9a-140">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="static-assets"></a><span data-ttu-id="afe9a-141">Les ressources statiques</span><span class="sxs-lookup"><span data-stu-id="afe9a-141">Static assets</span></span>

Blazor<span data-ttu-id="afe9a-142">suit la Convention de ASP.NET Core les applications qui placent des ressources statiques dans le [dossier racine Web (Wwwroot)](xref:fundamentals/index#web-root)du projet.</span><span class="sxs-lookup"><span data-stu-id="afe9a-142"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="afe9a-143">Utilisez un chemin d’accès relatif à `/` la base () pour faire référence à la racine Web d’une ressource statique.</span><span class="sxs-lookup"><span data-stu-id="afe9a-143">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="afe9a-144">Dans l’exemple suivant, *logo. png* se trouve physiquement dans le dossier *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="afe9a-144">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="afe9a-145">les composants ne prennent **pas** en charge la notation tilde-slash ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="afe9a-145"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="afe9a-146">Pour plus d’informations sur la définition du chemin d’accès de base d’une application, consultez <xref:host-and-deploy/blazor/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="afe9a-146">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="afe9a-147">Les balises tag Helper ne sont pas prises en charge dans les composants</span><span class="sxs-lookup"><span data-stu-id="afe9a-147">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="afe9a-148">Les [tag helpers](xref:mvc/views/tag-helpers/intro) ne sont pas pris en charge dans les Razor composants (fichiers *. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="afe9a-148">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="afe9a-149">Pour fournir des fonctionnalités semblables à tag Helper dans Blazor , créez un composant avec les mêmes fonctionnalités que le tag Helper et utilisez le composant à la place.</span><span class="sxs-lookup"><span data-stu-id="afe9a-149">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="afe9a-150">Utiliser des composants</span><span class="sxs-lookup"><span data-stu-id="afe9a-150">Use components</span></span>

<span data-ttu-id="afe9a-151">Les composants peuvent inclure d’autres composants en les déclarant à l’aide de la syntaxe d’élément HTML.</span><span class="sxs-lookup"><span data-stu-id="afe9a-151">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="afe9a-152">Le balisage pour l’utilisation d’un composant ressemble à une balise HTML où le nom de la balise est le type du composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-152">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="afe9a-153">Le balisage suivant dans *index. Razor* rend une `HeadingComponent` instance de :</span><span class="sxs-lookup"><span data-stu-id="afe9a-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="afe9a-154">*Composants/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-154">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="afe9a-155">Si un composant contient un élément HTML avec une première lettre majuscule qui ne correspond pas à un nom de composant, un avertissement est émis pour indiquer que l’élément a un nom inattendu.</span><span class="sxs-lookup"><span data-stu-id="afe9a-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="afe9a-156">L’ajout d’une `@using` directive pour l’espace de noms du composant rend le composant disponible, ce qui résout l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="afe9a-156">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="afe9a-157">Routage</span><span class="sxs-lookup"><span data-stu-id="afe9a-157">Routing</span></span>

<span data-ttu-id="afe9a-158">Le routage dans Blazor est effectué en fournissant un modèle de routage à chaque composant accessible dans l’application.</span><span class="sxs-lookup"><span data-stu-id="afe9a-158">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="afe9a-159">Lorsqu’un Razor fichier avec une `@page` directive est compilé, la classe générée reçoit un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> qui spécifie le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="afe9a-159">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="afe9a-160">Lors de l’exécution, le routeur recherche les classes de composant avec un `RouteAttribute` et rend le composant qui a un modèle de routage correspondant à l’URL demandée.</span><span class="sxs-lookup"><span data-stu-id="afe9a-160">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="afe9a-161">Pour plus d'informations, consultez <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="afe9a-161">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="afe9a-162">Paramètres</span><span class="sxs-lookup"><span data-stu-id="afe9a-162">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="afe9a-163">Paramètres d’itinéraire</span><span class="sxs-lookup"><span data-stu-id="afe9a-163">Route parameters</span></span>

<span data-ttu-id="afe9a-164">Les composants peuvent recevoir des paramètres de routage du modèle de routage fourni dans la `@page` directive.</span><span class="sxs-lookup"><span data-stu-id="afe9a-164">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="afe9a-165">Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-165">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="afe9a-166">*Pages/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-166">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="afe9a-167">Les paramètres facultatifs ne sont pas pris en charge `@page` . deux directives sont donc appliquées dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="afe9a-167">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="afe9a-168">La première permet de naviguer jusqu’au composant sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="afe9a-168">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="afe9a-169">La deuxième `@page` directive reçoit le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.</span><span class="sxs-lookup"><span data-stu-id="afe9a-169">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="afe9a-170">La syntaxe de paramètre *catch-all* ( `*` / `**` ), qui capture le chemin d’accès dans plusieurs limites de dossiers, n’est **pas** prise en charge dans les Razor composants (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="afe9a-170">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="afe9a-171">Paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="afe9a-171">Component parameters</span></span>

<span data-ttu-id="afe9a-172">Les composants peuvent avoir des *paramètres de composant*, qui sont définis à l’aide de propriétés publiques sur la classe de composant avec l' `[Parameter]` attribut.</span><span class="sxs-lookup"><span data-stu-id="afe9a-172">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="afe9a-173">Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.</span><span class="sxs-lookup"><span data-stu-id="afe9a-173">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="afe9a-174">*Composants/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-174">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="afe9a-175">Dans l’exemple suivant tiré de l’exemple d’application, le `ParentComponent` définit la valeur de la `Title` propriété de `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-175">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="afe9a-176">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-176">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="afe9a-177">Ne créez pas de composants qui écrivent dans leurs propres *paramètres de composant*, utilisez un champ privé à la place.</span><span class="sxs-lookup"><span data-stu-id="afe9a-177">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="afe9a-178">Pour plus d’informations, consultez la section [ne pas créer de composants qui écrivent dans leur propre propriété de paramètre](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="afe9a-178">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="afe9a-179">Contenu enfant</span><span class="sxs-lookup"><span data-stu-id="afe9a-179">Child content</span></span>

<span data-ttu-id="afe9a-180">Les composants peuvent définir le contenu d’un autre composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-180">Components can set the content of another component.</span></span> <span data-ttu-id="afe9a-181">Le composant d’affectation fournit le contenu entre les balises qui spécifient le composant récepteur.</span><span class="sxs-lookup"><span data-stu-id="afe9a-181">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="afe9a-182">Dans l’exemple suivant, `ChildComponent` a une `ChildContent` propriété qui représente un `RenderFragment` , qui représente un segment de l’interface utilisateur à restituer.</span><span class="sxs-lookup"><span data-stu-id="afe9a-182">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="afe9a-183">La valeur de `ChildContent` est positionnée dans le balisage du composant où le contenu doit être rendu.</span><span class="sxs-lookup"><span data-stu-id="afe9a-183">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="afe9a-184">La valeur de `ChildContent` est reçue du composant parent et rendue à l’intérieur du panneau de démarrage `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-184">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="afe9a-185">*Composants/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="afe9a-186">La propriété qui reçoit le `RenderFragment` contenu doit être nommée `ChildContent` par Convention.</span><span class="sxs-lookup"><span data-stu-id="afe9a-186">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="afe9a-187">`ParentComponent`Dans l’exemple d’application, vous pouvez fournir du contenu pour le rendu de `ChildComponent` en plaçant le contenu à l’intérieur des `<ChildComponent>` balises.</span><span class="sxs-lookup"><span data-stu-id="afe9a-187">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="afe9a-188">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-188">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="afe9a-189">Réprojection d’attribut et paramètres arbitraires</span><span class="sxs-lookup"><span data-stu-id="afe9a-189">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="afe9a-190">Les composants peuvent capturer et restituer des attributs supplémentaires en plus des paramètres déclarés du composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-190">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="afe9a-191">Des attributs supplémentaires peuvent être capturés dans un dictionnaire *, puis* réintégrés à un élément lorsque le composant est rendu à l’aide de la [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span><span class="sxs-lookup"><span data-stu-id="afe9a-191">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="afe9a-192">Ce scénario est utile lors de la définition d’un composant qui produit un élément de balisage qui prend en charge diverses personnalisations.</span><span class="sxs-lookup"><span data-stu-id="afe9a-192">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="afe9a-193">Par exemple, il peut être fastidieux de définir des attributs séparément pour un `<input>` qui prend en charge de nombreux paramètres.</span><span class="sxs-lookup"><span data-stu-id="afe9a-193">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="afe9a-194">Dans l’exemple suivant, le premier `<input>` élément ( `id="useIndividualParams"` ) utilise des paramètres de composant individuels, tandis que le deuxième `<input>` élément ( `id="useAttributesDict"` ) utilise la projection d’attributs :</span><span class="sxs-lookup"><span data-stu-id="afe9a-194">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="afe9a-195">Le type du paramètre doit implémenter `IEnumerable<KeyValuePair<string, object>>` avec des clés de chaîne.</span><span class="sxs-lookup"><span data-stu-id="afe9a-195">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="afe9a-196">L’utilisation `IReadOnlyDictionary<string, object>` de est également une option dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="afe9a-196">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="afe9a-197">Les éléments rendus `<input>` à l’aide des deux approches sont identiques :</span><span class="sxs-lookup"><span data-stu-id="afe9a-197">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="afe9a-198">Pour accepter des attributs arbitraires, définissez un paramètre de composant à l’aide de l' `[Parameter]` attribut avec la `CaptureUnmatchedValues` propriété définie sur `true` :</span><span class="sxs-lookup"><span data-stu-id="afe9a-198">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="afe9a-199">La `CaptureUnmatchedValues` propriété sur `[Parameter]` permet au paramètre de correspondre à tous les attributs qui ne correspondent à aucun autre paramètre.</span><span class="sxs-lookup"><span data-stu-id="afe9a-199">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="afe9a-200">Un composant ne peut définir qu’un seul paramètre avec `CaptureUnmatchedValues` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-200">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="afe9a-201">Le type de propriété utilisé avec `CaptureUnmatchedValues` doit pouvoir être assigné à partir de `Dictionary<string, object>` avec des clés de chaîne.</span><span class="sxs-lookup"><span data-stu-id="afe9a-201">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="afe9a-202">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` sont également des options dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="afe9a-202">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="afe9a-203">La position `@attributes` relative à la position des attributs d’élément est importante.</span><span class="sxs-lookup"><span data-stu-id="afe9a-203">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="afe9a-204">Quand sont représentées `@attributes` sur l’élément, les attributs sont traités de droite à gauche (dernier à premier).</span><span class="sxs-lookup"><span data-stu-id="afe9a-204">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="afe9a-205">Prenons l’exemple suivant d’un composant qui consomme un `Child` composant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-205">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="afe9a-206">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-206">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="afe9a-207">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-207">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="afe9a-208">L' `Child` attribut du composant `extra` est défini à droite de `@attributes` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-208">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="afe9a-209">Le `Parent` rendu du composant `<div>` contient `extra="5"` lorsqu’il est transmis via l’attribut supplémentaire, car les attributs sont traités de droite à gauche (dernier à premier) :</span><span class="sxs-lookup"><span data-stu-id="afe9a-209">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="afe9a-210">Dans l’exemple suivant, l’ordre de `extra` et `@attributes` est inversé dans le d' `Child` un composant `<div>` :</span><span class="sxs-lookup"><span data-stu-id="afe9a-210">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="afe9a-211">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-211">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="afe9a-212">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-212">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="afe9a-213">Le rendu `<div>` dans le `Parent` composant contient `extra="10"` lorsqu’il est passé par le biais de l’attribut supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="afe9a-213">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="afe9a-214">Capturer des références à des composants</span><span class="sxs-lookup"><span data-stu-id="afe9a-214">Capture references to components</span></span>

<span data-ttu-id="afe9a-215">Les références de composant offrent un moyen de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance, telles que `Show` ou `Reset` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-215">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="afe9a-216">Pour capturer une référence de composant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-216">To capture a component reference:</span></span>

* <span data-ttu-id="afe9a-217">Ajoutez un [`@ref`](xref:mvc/views/razor#ref) attribut au composant enfant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-217">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="afe9a-218">Définissez un champ avec le même type que le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-218">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="afe9a-219">Lors du rendu du composant, le `loginDialog` champ est rempli avec l' `MyLoginDialog` instance du composant enfant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-219">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="afe9a-220">Vous pouvez ensuite appeler des méthodes .NET sur l’instance du composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-220">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="afe9a-221">La `loginDialog` variable est remplie uniquement après le rendu du composant et sa sortie comprend l' `MyLoginDialog` élément.</span><span class="sxs-lookup"><span data-stu-id="afe9a-221">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="afe9a-222">Jusqu’à ce stade, il n’y a rien à référencer.</span><span class="sxs-lookup"><span data-stu-id="afe9a-222">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="afe9a-223">Pour manipuler des références de composants après la fin du rendu du composant, utilisez les [méthodes OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="afe9a-223">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="afe9a-224">Pour référencer des composants dans une boucle, consultez [capturer des références à plusieurs composants enfants similaires (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="afe9a-224">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="afe9a-225">Bien que la capture de références de composant utilise une syntaxe similaire pour [capturer des références d’élément](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), il ne s’agit pas d’une fonctionnalité JavaScript Interop.</span><span class="sxs-lookup"><span data-stu-id="afe9a-225">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="afe9a-226">Les références de composant ne sont pas transmises au code JavaScript et ne sont &mdash; utilisées que dans le code .net.</span><span class="sxs-lookup"><span data-stu-id="afe9a-226">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="afe9a-227">N’utilisez **pas** de références de composant pour muter l’état des composants enfants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-227">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="afe9a-228">Utilisez plutôt des paramètres déclaratifs normaux pour passer des données aux composants enfants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-228">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="afe9a-229">L’utilisation de paramètres déclaratifs normaux entraîne le rerendu automatique des composants enfants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-229">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="afe9a-230">Appeler des méthodes de composant en externe pour mettre à jour l’État</span><span class="sxs-lookup"><span data-stu-id="afe9a-230">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="afe9a-231">utilise un contexte de synchronisation ( `SynchronizationContext` ) pour appliquer un seul thread logique d’exécution.</span><span class="sxs-lookup"><span data-stu-id="afe9a-231"> uses a synchronization context (`SynchronizationContext`) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="afe9a-232">Les méthodes de [cycle de vie](xref:blazor/lifecycle) d’un composant et les rappels d’événements déclenchés par Blazor sont exécutés sur le contexte de synchronisation.</span><span class="sxs-lookup"><span data-stu-id="afe9a-232">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="afe9a-233">Le contexte de synchronisation du serveur tente d’émuler un environnement monothread afin qu’il corresponde étroitement au modèle webassembly dans le navigateur, qui est mono-thread.</span><span class="sxs-lookup"><span data-stu-id="afe9a-233"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="afe9a-234">À un moment donné, le travail est effectué sur un seul thread, ce qui donne l’impression d’un seul thread logique.</span><span class="sxs-lookup"><span data-stu-id="afe9a-234">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="afe9a-235">Deux opérations ne sont pas exécutées simultanément.</span><span class="sxs-lookup"><span data-stu-id="afe9a-235">No two operations execute concurrently.</span></span>

<span data-ttu-id="afe9a-236">Dans le cas où un composant doit être mis à jour en fonction d’un événement externe, tel qu’un minuteur ou d’autres notifications, utilisez la `InvokeAsync` méthode, qui effectue le dispatch dans le Blazor contexte de synchronisation de.</span><span class="sxs-lookup"><span data-stu-id="afe9a-236">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's synchronization context.</span></span> <span data-ttu-id="afe9a-237">Par exemple, considérez un *service de notification* qui peut notifier n’importe quel composant d’écoute de l’État mis à jour :</span><span class="sxs-lookup"><span data-stu-id="afe9a-237">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="afe9a-238">Inscrivez le `NotifierService` en tant que singletion :</span><span class="sxs-lookup"><span data-stu-id="afe9a-238">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="afe9a-239">Dans Blazor Webassembly, inscrivez le service dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="afe9a-239">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="afe9a-240">Dans Blazor serveur, inscrivez le service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="afe9a-240">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="afe9a-241">Utilisez `NotifierService` pour mettre à jour un composant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-241">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="afe9a-242">Dans l’exemple précédent, `NotifierService` appelle la méthode du composant `OnNotify` en dehors du Blazor contexte de synchronisation de.</span><span class="sxs-lookup"><span data-stu-id="afe9a-242">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="afe9a-243">`InvokeAsync`est utilisé pour basculer vers le contexte correct et pour la mise en file d’attente d’un rendu.</span><span class="sxs-lookup"><span data-stu-id="afe9a-243">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="afe9a-244">Utiliser \@ la clé pour contrôler la conservation des éléments et des composants</span><span class="sxs-lookup"><span data-stu-id="afe9a-244">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="afe9a-245">Lors du rendu d’une liste d’éléments ou de composants, et si les éléments ou les composants changent par la suite, l' Blazor algorithme de différenciation de doit décider quels éléments ou composants précédents peuvent être conservés et comment les objets de modèle doivent être mappés à eux.</span><span class="sxs-lookup"><span data-stu-id="afe9a-245">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="afe9a-246">Normalement, ce processus est automatique et peut être ignoré, mais dans certains cas, il peut être utile de contrôler le processus.</span><span class="sxs-lookup"><span data-stu-id="afe9a-246">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="afe9a-247">Prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-247">Consider the following example:</span></span>

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

<span data-ttu-id="afe9a-248">Le contenu de la `People` collection peut changer avec des entrées insérées, supprimées ou réorganisées.</span><span class="sxs-lookup"><span data-stu-id="afe9a-248">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="afe9a-249">Lors du rerendu du composant, le `<DetailsEditor>` composant peut changer pour recevoir des `Details` valeurs de paramètre différentes.</span><span class="sxs-lookup"><span data-stu-id="afe9a-249">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="afe9a-250">Cela peut entraîner un rerendu plus complexe que prévu.</span><span class="sxs-lookup"><span data-stu-id="afe9a-250">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="afe9a-251">Dans certains cas, le rerendu peut entraîner des différences de comportement visibles, telles que le focus de l’élément perdu.</span><span class="sxs-lookup"><span data-stu-id="afe9a-251">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="afe9a-252">Le processus de mappage peut être contrôlé à l’aide de l' [`@key`](xref:mvc/views/razor#key) attribut directive.</span><span class="sxs-lookup"><span data-stu-id="afe9a-252">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="afe9a-253">`@key`force l’algorithme de comparaison à garantir la préservation des éléments ou des composants en fonction de la valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="afe9a-253">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="afe9a-254">Lorsque la `People` collection est modifiée, l’algorithme de comparaison conserve l’association entre les `<DetailsEditor>` instances et les `person` instances :</span><span class="sxs-lookup"><span data-stu-id="afe9a-254">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="afe9a-255">Si un `Person` est supprimé de la `People` liste, seule l' `<DetailsEditor>` instance correspondante est supprimée de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="afe9a-255">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="afe9a-256">Les autres instances restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="afe9a-256">Other instances are left unchanged.</span></span>
* <span data-ttu-id="afe9a-257">Si un `Person` est inséré à une position dans la liste, une nouvelle `<DetailsEditor>` instance est insérée à cette position correspondante.</span><span class="sxs-lookup"><span data-stu-id="afe9a-257">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="afe9a-258">Les autres instances restent inchangées.</span><span class="sxs-lookup"><span data-stu-id="afe9a-258">Other instances are left unchanged.</span></span>
* <span data-ttu-id="afe9a-259">Si `Person` les entrées sont réordonnées, les `<DetailsEditor>` instances correspondantes sont conservées et réordonnées dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="afe9a-259">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="afe9a-260">Dans certains scénarios, l’utilisation de `@key` réduit la complexité du rerendu et évite les problèmes potentiels liés aux parties avec état de la modification du modèle DOM, telles que la position du focus.</span><span class="sxs-lookup"><span data-stu-id="afe9a-260">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="afe9a-261">Les clés sont locales pour chaque élément ou composant conteneur.</span><span class="sxs-lookup"><span data-stu-id="afe9a-261">Keys are local to each container element or component.</span></span> <span data-ttu-id="afe9a-262">Les clés ne sont pas comparées globalement dans le document.</span><span class="sxs-lookup"><span data-stu-id="afe9a-262">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="afe9a-263">Quand utiliser la \@ clé</span><span class="sxs-lookup"><span data-stu-id="afe9a-263">When to use \@key</span></span>

<span data-ttu-id="afe9a-264">En règle générale, il est judicieux d’utiliser `@key` chaque fois qu’une liste est rendue (par exemple, dans un `@foreach` bloc) et qu’une valeur appropriée existe pour définir `@key` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-264">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="afe9a-265">Vous pouvez également utiliser `@key` pour empêcher Blazor de conserver un élément ou une sous-arborescence de composants lorsqu’un objet change :</span><span class="sxs-lookup"><span data-stu-id="afe9a-265">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="afe9a-266">En cas `@currentPerson` de modification, la `@key` directive Blazor d’attribut force à ignorer la totalité `<div>` et ses descendants, et à reconstruire la sous-arborescence de l’interface utilisateur avec de nouveaux éléments et composants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-266">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="afe9a-267">Cela peut être utile si vous devez garantir qu’aucun État d’interface utilisateur n’est préservé lorsque des `@currentPerson` modifications sont apportées.</span><span class="sxs-lookup"><span data-stu-id="afe9a-267">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="afe9a-268">Quand ne pas utiliser la \@ clé</span><span class="sxs-lookup"><span data-stu-id="afe9a-268">When not to use \@key</span></span>

<span data-ttu-id="afe9a-269">Il y a un coût en matière de performances lors de la comparaison avec `@key` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-269">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="afe9a-270">Le coût des performances n’est pas important, mais spécifiez uniquement `@key` si les règles de conservation des éléments ou des composants bénéficient de l’application.</span><span class="sxs-lookup"><span data-stu-id="afe9a-270">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="afe9a-271">Même si `@key` n’est pas utilisé, Blazor conserve autant que possible les instances d’élément et de composant enfants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-271">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="afe9a-272">Le seul avantage de l’utilisation de `@key` est de contrôler la *façon dont* les instances de modèle sont mappées aux instances de composant conservées, au lieu de l’algorithme de comparaison qui sélectionne le mappage.</span><span class="sxs-lookup"><span data-stu-id="afe9a-272">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="afe9a-273">Valeurs à utiliser pour la \@ clé</span><span class="sxs-lookup"><span data-stu-id="afe9a-273">What values to use for \@key</span></span>

<span data-ttu-id="afe9a-274">En règle générale, il est logique de fournir l’un des types de valeur suivants pour `@key` :</span><span class="sxs-lookup"><span data-stu-id="afe9a-274">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="afe9a-275">Instances d’objet de modèle (par exemple, une `Person` instance comme dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="afe9a-275">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="afe9a-276">Cela garantit la préservation en fonction de l’égalité des références d’objet.</span><span class="sxs-lookup"><span data-stu-id="afe9a-276">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="afe9a-277">Identificateurs uniques (par exemple, les valeurs de clé primaire de type `int` , `string` ou `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="afe9a-277">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="afe9a-278">Vérifiez que les valeurs utilisées pour `@key` ne sont pas en conflit.</span><span class="sxs-lookup"><span data-stu-id="afe9a-278">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="afe9a-279">Si les valeurs en conflit sont détectées dans le même élément parent, Blazor lève une exception, car il ne peut pas mapper de manière déterministe les anciens éléments ou composants aux nouveaux éléments ou composants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-279">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="afe9a-280">Utilisez uniquement des valeurs distinctes, telles que des instances d’objets ou des valeurs de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="afe9a-280">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="afe9a-281">Ne créez pas de composants qui écrivent dans leurs propres propriétés de paramètre</span><span class="sxs-lookup"><span data-stu-id="afe9a-281">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="afe9a-282">Les paramètres sont remplacés dans les conditions suivantes :</span><span class="sxs-lookup"><span data-stu-id="afe9a-282">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="afe9a-283">Le contenu d’un composant enfant est rendu avec un `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-283">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="afe9a-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé dans le composant parent.</span><span class="sxs-lookup"><span data-stu-id="afe9a-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="afe9a-285">Les paramètres sont réinitialisés, car le composant parent <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est restitué à nouveau lorsque est appelé et de nouvelles valeurs de paramètres sont fournies au composant enfant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-285">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="afe9a-286">Prenons le `Expander` composant suivant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-286">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="afe9a-287">Restitue le contenu enfant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-287">Renders child content.</span></span>
* <span data-ttu-id="afe9a-288">Active ou désactive l’indication du contenu enfant avec un paramètre de composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-288">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="afe9a-289">Le `Expander` composant est ajouté à un composant parent qui peut appeler `StateHasChanged` :</span><span class="sxs-lookup"><span data-stu-id="afe9a-289">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="afe9a-290">Au départ, les `Expander` composants se comportent indépendamment lorsque leurs `Expanded` propriétés sont basculées.</span><span class="sxs-lookup"><span data-stu-id="afe9a-290">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="afe9a-291">Les composants enfants maintiennent leurs États comme prévu.</span><span class="sxs-lookup"><span data-stu-id="afe9a-291">The child components maintain their states as expected.</span></span> <span data-ttu-id="afe9a-292">Lorsque `StateHasChanged` est appelé dans le parent, le `Expanded` paramètre du premier composant enfant est réinitialisé à sa valeur initiale ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="afe9a-292">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="afe9a-293">La `Expander` valeur du deuxième composant `Expanded` n’est pas réinitialisée, car aucun contenu enfant n’est restitué dans le deuxième composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-293">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="afe9a-294">Pour maintenir l’État dans le scénario précédent, utilisez un *champ privé* dans le `Expander` composant pour maintenir son état bascule.</span><span class="sxs-lookup"><span data-stu-id="afe9a-294">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="afe9a-295">Le `Expander` composant suivant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-295">The following `Expander` component:</span></span>

* <span data-ttu-id="afe9a-296">Accepte la `Expanded` valeur de paramètre du composant à partir du parent.</span><span class="sxs-lookup"><span data-stu-id="afe9a-296">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="afe9a-297">Affecte la valeur du paramètre de composant à un *champ privé* ( `expanded` ) dans l' [événement OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="afe9a-297">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="afe9a-298">Utilise le champ privé pour conserver son état bascule interne.</span><span class="sxs-lookup"><span data-stu-id="afe9a-298">Uses the private field to maintain its internal toggle state.</span></span>

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

## <a name="partial-class-support"></a><span data-ttu-id="afe9a-299">Prise en charge des classes partielles</span><span class="sxs-lookup"><span data-stu-id="afe9a-299">Partial class support</span></span>

Razor<span data-ttu-id="afe9a-300">les composants sont générés en tant que classes partielles.</span><span class="sxs-lookup"><span data-stu-id="afe9a-300"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="afe9a-301">les composants sont créés à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="afe9a-301"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="afe9a-302">Le code C# est défini dans un [`@code`](xref:mvc/views/razor#code) bloc avec le balisage HTML et Razor le code dans un fichier unique.</span><span class="sxs-lookup"><span data-stu-id="afe9a-302">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="afe9a-303">les modèles définissent leurs Razor composants à l’aide de cette approche.</span><span class="sxs-lookup"><span data-stu-id="afe9a-303"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="afe9a-304">Le code C# est placé dans un fichier code-behind défini en tant que classe partielle.</span><span class="sxs-lookup"><span data-stu-id="afe9a-304">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="afe9a-305">L’exemple suivant montre le composant par défaut `Counter` avec un `@code` bloc dans une application générée à partir d’un Blazor modèle.</span><span class="sxs-lookup"><span data-stu-id="afe9a-305">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="afe9a-306">Le balisage HTML, le Razor code et le code C# se trouvent dans le même fichier :</span><span class="sxs-lookup"><span data-stu-id="afe9a-306">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="afe9a-307">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-307">*Counter.razor*:</span></span>

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

<span data-ttu-id="afe9a-308">Le `Counter` composant peut également être créé à l’aide d’un fichier code-behind avec une classe partielle :</span><span class="sxs-lookup"><span data-stu-id="afe9a-308">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="afe9a-309">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-309">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="afe9a-310">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-310">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="afe9a-311">Ajoutez tous les espaces de noms requis au fichier de classe partielle, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="afe9a-311">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="afe9a-312">Les espaces de noms standard utilisés par les Razor composants sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="afe9a-312">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="afe9a-313">Spécifier une classe de base</span><span class="sxs-lookup"><span data-stu-id="afe9a-313">Specify a base class</span></span>

<span data-ttu-id="afe9a-314">La [`@inherits`](xref:mvc/views/razor#inherits) directive peut être utilisée pour spécifier une classe de base pour un composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-314">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="afe9a-315">L’exemple suivant montre comment un composant peut hériter d’une classe de base, `BlazorRocksBase` , pour fournir les propriétés et les méthodes du composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-315">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="afe9a-316">La classe de base doit dériver de `ComponentBase` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-316">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="afe9a-317">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-317">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="afe9a-318">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-318">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="afe9a-319">Spécifier un attribut</span><span class="sxs-lookup"><span data-stu-id="afe9a-319">Specify an attribute</span></span>

<span data-ttu-id="afe9a-320">Les attributs peuvent être spécifiés dans Razor les composants à l’aide de la [`@attribute`](xref:mvc/views/razor#attribute) directive.</span><span class="sxs-lookup"><span data-stu-id="afe9a-320">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="afe9a-321">L’exemple suivant applique l' `[Authorize]` attribut à la classe de composant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-321">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="afe9a-322">Importer des composants</span><span class="sxs-lookup"><span data-stu-id="afe9a-322">Import components</span></span>

<span data-ttu-id="afe9a-323">L’espace de noms d’un composant créé avec Razor est basé sur (par ordre de priorité) :</span><span class="sxs-lookup"><span data-stu-id="afe9a-323">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="afe9a-324">[`@namespace`](xref:mvc/views/razor#namespace)désignation dans le Razor balisage de fichier (*. Razor*) ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="afe9a-324">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="afe9a-325">Du projet `RootNamespace` dans le fichier projet ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="afe9a-325">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="afe9a-326">Nom du projet, pris à partir du nom de fichier du fichier projet (*. csproj*), et chemin d’accès de la racine du projet au composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-326">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="afe9a-327">Par exemple, le Framework résout *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) en espace de noms `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-327">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="afe9a-328">Les composants suivent les règles de liaison de noms C#.</span><span class="sxs-lookup"><span data-stu-id="afe9a-328">Components follow C# name binding rules.</span></span> <span data-ttu-id="afe9a-329">Pour le `Index` composant dans cet exemple, les composants de l’étendue sont tous les composants :</span><span class="sxs-lookup"><span data-stu-id="afe9a-329">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="afe9a-330">Dans le même dossier, *pages*.</span><span class="sxs-lookup"><span data-stu-id="afe9a-330">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="afe9a-331">Composants de la racine du projet qui ne spécifient pas explicitement un espace de noms différent.</span><span class="sxs-lookup"><span data-stu-id="afe9a-331">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="afe9a-332">Les composants définis dans un espace de noms différent sont placés dans la portée à l’aide Razor de [`@using`](xref:mvc/views/razor#using) la directive de.</span><span class="sxs-lookup"><span data-stu-id="afe9a-332">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="afe9a-333">Si un autre composant, `NavMenu.razor` , existe dans le dossier *BlazorSample/Shared/* , le composant peut être utilisé dans `Index.razor` avec l' `@using` instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="afe9a-333">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="afe9a-334">Les composants peuvent également être référencés à l’aide de leurs noms complets, ce qui ne requiert pas la [`@using`](xref:mvc/views/razor#using) directive :</span><span class="sxs-lookup"><span data-stu-id="afe9a-334">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="afe9a-335">La `global::` qualification n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="afe9a-335">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="afe9a-336">L’importation de composants avec des instructions avec alias `using` (par exemple, `@using Foo = Bar` ) n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="afe9a-336">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="afe9a-337">Les noms partiellement qualifiés ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="afe9a-337">Partially qualified names aren't supported.</span></span> <span data-ttu-id="afe9a-338">Par exemple, l’ajout `@using BlazorSample` et la référencement `NavMenu.razor` avec `<Shared.NavMenu></Shared.NavMenu>` ne sont pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="afe9a-338">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="afe9a-339">Attributs d’éléments HTML conditionnels</span><span class="sxs-lookup"><span data-stu-id="afe9a-339">Conditional HTML element attributes</span></span>

<span data-ttu-id="afe9a-340">Les attributs des éléments HTML sont rendus de manière conditionnelle en fonction de la valeur .NET.</span><span class="sxs-lookup"><span data-stu-id="afe9a-340">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="afe9a-341">Si la valeur est `false` ou `null` , l’attribut n’est pas rendu.</span><span class="sxs-lookup"><span data-stu-id="afe9a-341">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="afe9a-342">Si la valeur est `true` , l’attribut est rendu réduit.</span><span class="sxs-lookup"><span data-stu-id="afe9a-342">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="afe9a-343">Dans l’exemple suivant, `IsCompleted` détermine si `checked` est rendu dans le balisage de l’élément :</span><span class="sxs-lookup"><span data-stu-id="afe9a-343">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="afe9a-344">Si `IsCompleted` est `true` , la case à cocher s’affiche comme suit :</span><span class="sxs-lookup"><span data-stu-id="afe9a-344">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="afe9a-345">Si `IsCompleted` est `false` , la case à cocher s’affiche comme suit :</span><span class="sxs-lookup"><span data-stu-id="afe9a-345">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="afe9a-346">Pour plus d'informations, consultez <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="afe9a-346">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="afe9a-347">Certains attributs HTML, tels que [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), ne fonctionnent pas correctement quand le type .net est `bool` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-347">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="afe9a-348">Dans ce cas, utilisez un `string` type au lieu d’un `bool` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-348">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="afe9a-349">HTML brut</span><span class="sxs-lookup"><span data-stu-id="afe9a-349">Raw HTML</span></span>

<span data-ttu-id="afe9a-350">Les chaînes sont normalement rendues à l’aide de nœuds de texte DOM, ce qui signifie que tout balisage qu’elles peuvent contenir est ignorée et traitée comme du texte littéral.</span><span class="sxs-lookup"><span data-stu-id="afe9a-350">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="afe9a-351">Pour afficher le code HTML brut, encapsulez le contenu HTML dans une `MarkupString` valeur.</span><span class="sxs-lookup"><span data-stu-id="afe9a-351">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="afe9a-352">La valeur est analysée au format HTML ou SVG et est insérée dans le DOM.</span><span class="sxs-lookup"><span data-stu-id="afe9a-352">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="afe9a-353">Le rendu de code HTML brut construit à partir de n’importe quelle source non approuvée constitue un risque pour la **sécurité** et doit être évité !</span><span class="sxs-lookup"><span data-stu-id="afe9a-353">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="afe9a-354">L’exemple suivant illustre l’utilisation du `MarkupString` type pour ajouter un bloc de contenu HTML statique à la sortie rendue d’un composant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-354">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="afe9a-355">Valeurs et paramètres en cascade</span><span class="sxs-lookup"><span data-stu-id="afe9a-355">Cascading values and parameters</span></span>

<span data-ttu-id="afe9a-356">Dans certains scénarios, il est peu commode de transmettre des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composant](#component-parameters), en particulier lorsqu’il existe plusieurs couches de composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-356">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="afe9a-357">Les valeurs et paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant ancêtre de fournir une valeur à tous ses composants descendants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-357">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="afe9a-358">Les valeurs et les paramètres en cascade fournissent également une approche pour les composants à coordonner.</span><span class="sxs-lookup"><span data-stu-id="afe9a-358">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="afe9a-359">Exemple de thème</span><span class="sxs-lookup"><span data-stu-id="afe9a-359">Theme example</span></span>

<span data-ttu-id="afe9a-360">Dans l’exemple suivant tiré de l’exemple d’application, la `ThemeInfo` classe spécifie les informations de thème pour descendre dans la hiérarchie des composants, de sorte que tous les boutons d’une partie donnée de l’application partagent le même style.</span><span class="sxs-lookup"><span data-stu-id="afe9a-360">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="afe9a-361">*UIThemeClasses/themeinfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="afe9a-361">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="afe9a-362">Un composant ancêtre peut fournir une valeur en cascade à l’aide du composant de valeur en cascade.</span><span class="sxs-lookup"><span data-stu-id="afe9a-362">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="afe9a-363">Le `CascadingValue` composant encapsule une sous-arborescence de la hiérarchie des composants et fournit une valeur unique à tous les composants de cette sous-arborescence.</span><span class="sxs-lookup"><span data-stu-id="afe9a-363">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="afe9a-364">Par exemple, l’exemple d’application spécifie les informations de thème ( `ThemeInfo` ) dans l’une des dispositions de l’application en tant que paramètre en cascade pour tous les composants qui composent le corps de la disposition de la `@Body` propriété.</span><span class="sxs-lookup"><span data-stu-id="afe9a-364">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="afe9a-365">`ButtonClass`la valeur est affectée `btn-success` à dans le composant Layout.</span><span class="sxs-lookup"><span data-stu-id="afe9a-365">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="afe9a-366">Tout composant descendant peut consommer cette propriété par le biais de l' `ThemeInfo` objet en cascade.</span><span class="sxs-lookup"><span data-stu-id="afe9a-366">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="afe9a-367">`CascadingValuesParametersLayout`-</span><span class="sxs-lookup"><span data-stu-id="afe9a-367">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="afe9a-368">Pour utiliser des valeurs en cascade, les composants déclarent des paramètres en cascade à l’aide de l' `[CascadingParameter]` attribut.</span><span class="sxs-lookup"><span data-stu-id="afe9a-368">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="afe9a-369">Les valeurs en cascade sont liées aux paramètres en cascade par type.</span><span class="sxs-lookup"><span data-stu-id="afe9a-369">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="afe9a-370">Dans l’exemple d’application, le `CascadingValuesParametersTheme` composant lie la `ThemeInfo` valeur en cascade à un paramètre en cascade.</span><span class="sxs-lookup"><span data-stu-id="afe9a-370">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="afe9a-371">Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-371">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="afe9a-372">`CascadingValuesParametersTheme`-</span><span class="sxs-lookup"><span data-stu-id="afe9a-372">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="afe9a-373">Pour mettre en cascade plusieurs valeurs du même type dans la même sous-arborescence, fournissez une `Name` chaîne unique à chaque `CascadingValue` composant et à son correspondant `CascadingParameter` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-373">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="afe9a-374">Dans l’exemple suivant, deux `CascadingValue` composants montent en cascade différentes instances de `MyCascadingType` par nom :</span><span class="sxs-lookup"><span data-stu-id="afe9a-374">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="afe9a-375">Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans le composant ancêtre par nom :</span><span class="sxs-lookup"><span data-stu-id="afe9a-375">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="afe9a-376">Exemple TabSet</span><span class="sxs-lookup"><span data-stu-id="afe9a-376">TabSet example</span></span>

<span data-ttu-id="afe9a-377">Les paramètres en cascade permettent également aux composants de collaborer au sein de la hiérarchie des composants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-377">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="afe9a-378">Par exemple, considérez l’exemple *TabSet* suivant dans l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="afe9a-378">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="afe9a-379">L’exemple d’application possède une `ITab` interface qui implémente les onglets suivants :</span><span class="sxs-lookup"><span data-stu-id="afe9a-379">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="afe9a-380">Le `CascadingValuesParametersTabSet` composant utilise le `TabSet` composant, qui contient plusieurs `Tab` composants :</span><span class="sxs-lookup"><span data-stu-id="afe9a-380">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="afe9a-381">Les `Tab` composants enfants ne sont pas explicitement passés comme paramètres à `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-381">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="afe9a-382">Au lieu de cela, les `Tab` composants enfants font partie du contenu enfant de `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="afe9a-382">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="afe9a-383">Toutefois, le `TabSet` doit toujours connaître chaque `Tab` composant pour pouvoir afficher les en-têtes et l’onglet actif. Pour activer cette coordination sans nécessiter de code supplémentaire, le `TabSet` composant *peut se présenter comme une valeur en cascade* qui est ensuite récupérée par les `Tab` composants descendants.</span><span class="sxs-lookup"><span data-stu-id="afe9a-383">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="afe9a-384">`TabSet`-</span><span class="sxs-lookup"><span data-stu-id="afe9a-384">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="afe9a-385">Les composants descendants `Tab` capturent le contenant `TabSet` comme paramètre en cascade, de sorte que les `Tab` composants s’ajoutent eux-mêmes à la et à la `TabSet` coordonnée sur laquelle l’onglet est actif.</span><span class="sxs-lookup"><span data-stu-id="afe9a-385">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="afe9a-386">`Tab`-</span><span class="sxs-lookup"><span data-stu-id="afe9a-386">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="afe9a-387">ceux</span><span class="sxs-lookup"><span data-stu-id="afe9a-387"> templates</span></span>

<span data-ttu-id="afe9a-388">Les fragments de rendu peuvent être définis à l’aide de la Razor syntaxe de modèle.</span><span class="sxs-lookup"><span data-stu-id="afe9a-388">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="afe9a-389">les modèles sont un moyen de définir un extrait de code d’interface utilisateur et de supposer le format suivant :</span><span class="sxs-lookup"><span data-stu-id="afe9a-389"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="afe9a-390">L’exemple suivant montre comment spécifier `RenderFragment` des valeurs et `RenderFragment<T>` et restituer des modèles directement dans un composant.</span><span class="sxs-lookup"><span data-stu-id="afe9a-390">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="afe9a-391">Les fragments de rendu peuvent également être passés comme arguments à des [composants basés](xref:blazor/templated-components)sur un modèle.</span><span class="sxs-lookup"><span data-stu-id="afe9a-391">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="afe9a-392">Sortie rendue du code précédent :</span><span class="sxs-lookup"><span data-stu-id="afe9a-392">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="afe9a-393">Images SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="afe9a-393">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="afe9a-394">Étant donné que Blazor le rendu des images HTML, prises en charge par le navigateur, y compris les images SVG (Scalable Vector Graphics) (*. svg*), est pris en charge via la `<img>` balise :</span><span class="sxs-lookup"><span data-stu-id="afe9a-394">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="afe9a-395">De même, les images SVG sont prises en charge dans les règles CSS d’un fichier de feuille de style (*. CSS*) :</span><span class="sxs-lookup"><span data-stu-id="afe9a-395">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="afe9a-396">Toutefois, le balisage SVG en ligne n’est pas pris en charge dans tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="afe9a-396">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="afe9a-397">Si vous placez une `<svg>` balise directement dans un fichier de composant (*. Razor*), le rendu d’image de base est pris en charge, mais de nombreux scénarios avancés ne sont pas encore pris en charge.</span><span class="sxs-lookup"><span data-stu-id="afe9a-397">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="afe9a-398">Par exemple, les `<use>` balises ne sont pas actuellement respectées et `@bind` ne peuvent pas être utilisées avec certaines balises SVG.</span><span class="sxs-lookup"><span data-stu-id="afe9a-398">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="afe9a-399">Nous prévoyons de traiter ces limitations dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="afe9a-399">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="afe9a-400">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="afe9a-400">Additional resources</span></span>

* <span data-ttu-id="afe9a-401"><xref:security/blazor/server/threat-mitigation>&ndash;Contient des conseils sur la création Blazor Applications serveur qui doivent rivaliser avec l’épuisement des ressources.</span><span class="sxs-lookup"><span data-stu-id="afe9a-401"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
