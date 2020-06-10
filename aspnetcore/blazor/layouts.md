---
title: Dispositions de ASP.NET Core Blazor
author: guardrex
description: Découvrez comment créer des composants de disposition réutilisables pour les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: ba172282d0cd6371ebc94b4fda1c13aee14d6fbd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851990"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="5b6fc-103">Dispositions de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5b6fc-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="5b6fc-104">Par [Rainer Stropek](https://www.timecockpit.com) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5b6fc-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5b6fc-105">Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la mise en page globale de l’application et sont utilisés par chaque composant de l’application.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="5b6fc-106">La copie du code de ces éléments dans tous les composants d’une application n’est pas une approche efficace.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="5b6fc-107">Chaque fois que l’un des éléments requiert une mise à jour, chaque composant doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="5b6fc-108">Une telle duplication est difficile à gérer et peut entraîner une incohérence du contenu au fil du temps.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="5b6fc-109">Les *dispositions* résolvent ce problème.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="5b6fc-110">Techniquement, une disposition est simplement un autre composant.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="5b6fc-111">Une disposition est définie dans un Razor modèle ou dans du code C# et peut utiliser la [liaison de données](xref:blazor/data-binding), l' [injection de dépendances](xref:blazor/dependency-injection)et d’autres scénarios de composants.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="5b6fc-112">Pour transformer un *composant* en une *disposition*, le composant :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="5b6fc-113">Hérite de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , qui définit une <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété pour le contenu rendu à l’intérieur de la disposition.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="5b6fc-114">Utilise la Razor syntaxe `@Body` pour spécifier l’emplacement dans la balise de mise en page où le contenu est restitué.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="5b6fc-115">L’exemple de code suivant montre le Razor modèle d’un composant de disposition, *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-115">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="5b6fc-116">La disposition hérite <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> de et définit la `@Body` entre la barre de navigation et le pied de page :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="5b6fc-117">Dans une application basée sur l’un des Blazor modèles d’application, le `MainLayout` composant (*MainLayout. Razor*) se trouve dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-117">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="5b6fc-118">Disposition par défaut</span><span class="sxs-lookup"><span data-stu-id="5b6fc-118">Default layout</span></span>

<span data-ttu-id="5b6fc-119">Spécifiez la disposition de l’application par défaut dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant dans le fichier *app. Razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-119">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's *App.razor* file.</span></span> <span data-ttu-id="5b6fc-120">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant suivant, qui est fourni par les modèles par défaut Blazor , définit la disposition par défaut sur le `MainLayout` composant :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-120">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="5b6fc-121">Pour fournir une disposition par défaut pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, spécifiez un <xref:Microsoft.AspNetCore.Components.LayoutView> pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-121">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="5b6fc-122">Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, consultez <xref:blazor/routing> .</span><span class="sxs-lookup"><span data-stu-id="5b6fc-122">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="5b6fc-123">La spécification de la disposition comme disposition par défaut dans le routeur est une pratique utile, car elle peut être remplacée par composant ou par dossier.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-123">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="5b6fc-124">Préférez utiliser le routeur pour définir la disposition par défaut de l’application, car il s’agit de la technique la plus générale.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-124">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="5b6fc-125">Spécifier une disposition dans un composant</span><span class="sxs-lookup"><span data-stu-id="5b6fc-125">Specify a layout in a component</span></span>

<span data-ttu-id="5b6fc-126">Utilisez la Razor directive `@layout` pour appliquer une disposition à un composant.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-126">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="5b6fc-127">Le compilateur convertit `@layout` en <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , qui est appliqué à la classe de composant.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-127">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="5b6fc-128">Le contenu du composant suivant `MasterList` est inséré dans le `MasterLayout` à la position de `@Body` :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-128">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="5b6fc-129">La spécification de la disposition directement dans un composant remplace un ensemble de *dispositions par défaut* dans le routeur ou une `@layout` directive importée à partir de *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-129">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="5b6fc-130">Sélection de la disposition centralisée</span><span class="sxs-lookup"><span data-stu-id="5b6fc-130">Centralized layout selection</span></span>

<span data-ttu-id="5b6fc-131">Chaque dossier d’une application peut éventuellement contenir un fichier modèle nommé *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-131">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="5b6fc-132">Le compilateur comprend les directives spécifiées dans le fichier Imports dans tous les Razor modèles du même dossier et de manière récursive dans tous ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-132">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="5b6fc-133">Par conséquent, un fichier *_Imports. Razor* contenant `@layout MyCoolLayout` s’assure que tous les composants d’un dossier utilisent `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="5b6fc-133">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="5b6fc-134">Il n’est pas nécessaire d’ajouter à plusieurs reprises `@layout MyCoolLayout` à tous les fichiers *. Razor* dans le dossier et les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-134">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="5b6fc-135">`@using`les directives sont également appliquées aux composants de la même façon.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-135">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="5b6fc-136">Le fichier *_Imports. Razor* suivant importe les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-136">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="5b6fc-137">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-137">`MyCoolLayout`.</span></span>
* <span data-ttu-id="5b6fc-138">Tous les Razor composants du même dossier et de tous les sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-138">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="5b6fc-139">Espace de noms `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-139">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="5b6fc-140">Le fichier *_Imports. Razor* est semblable au [fichier _ViewImports. cshtml pour les Razor affichages et les pages,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux Razor fichiers de composants.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-140">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="5b6fc-141">La spécification d’une disposition dans *_Imports. Razor* remplace une disposition spécifiée comme *disposition par défaut*du routeur.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-141">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="5b6fc-142">Dispositions imbriquées</span><span class="sxs-lookup"><span data-stu-id="5b6fc-142">Nested layouts</span></span>

<span data-ttu-id="5b6fc-143">Les applications peuvent se composer de dispositions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-143">Apps can consist of nested layouts.</span></span> <span data-ttu-id="5b6fc-144">Un composant peut faire référence à une disposition qui, à son tour, fait référence à une autre disposition.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-144">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="5b6fc-145">Par exemple, les dispositions d’imbrication sont utilisées pour créer une structure de menus à plusieurs niveaux.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-145">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="5b6fc-146">L’exemple suivant montre comment utiliser des dispositions imbriquées.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-146">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="5b6fc-147">Le fichier *EpisodesComponent. Razor* est le composant à afficher.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-147">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="5b6fc-148">Le composant fait référence à `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-148">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="5b6fc-149">Le fichier *MasterListLayout. Razor* fournit le `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="5b6fc-149">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="5b6fc-150">La disposition fait référence à une autre disposition, `MasterLayout` , où elle est rendue.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-150">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="5b6fc-151">`EpisodesComponent`l’emplacement est affiché `@Body` :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-151">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="5b6fc-152">Enfin, `MasterLayout` dans *MasterLayout. Razor* contient les éléments de disposition de niveau supérieur, tels que l’en-tête, le menu principal et le pied de page.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-152">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="5b6fc-153">`MasterListLayout`avec `EpisodesComponent` est rendu où `@Body` s’affiche :</span><span class="sxs-lookup"><span data-stu-id="5b6fc-153">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="5b6fc-154">Partager une Razor disposition de pages avec des composants intégrés</span><span class="sxs-lookup"><span data-stu-id="5b6fc-154">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="5b6fc-155">Lorsque des composants routables sont intégrés à une Razor application pages, la disposition partagée de l’application peut être utilisée avec les composants.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-155">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="5b6fc-156">Pour plus d’informations, consultez <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="5b6fc-156">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b6fc-157">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5b6fc-157">Additional resources</span></span>

* <xref:mvc/views/layout>
