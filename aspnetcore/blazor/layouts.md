---
title: ASP.NET mises en Blazor page De base
author: guardrex
description: Découvrez comment créer des composants de Blazor mise en page réutilisables pour les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660411"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="85c7f-103">ASP.NET mises en Blazor page De base</span><span class="sxs-lookup"><span data-stu-id="85c7f-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="85c7f-104">Par [Rainer Stropek](https://www.timecockpit.com) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="85c7f-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="85c7f-105">Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la mise en page globale de l’application et sont utilisés par chaque composant de l’application.</span><span class="sxs-lookup"><span data-stu-id="85c7f-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="85c7f-106">Copier le code de ces éléments dans tous les composants d’une application n’est pas une approche&mdash;efficace chaque fois que l’un des éléments nécessite une mise à jour, chaque composant doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="85c7f-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="85c7f-107">Une telle duplication est difficile à maintenir et peut conduire à un contenu incohérent au fil du temps.</span><span class="sxs-lookup"><span data-stu-id="85c7f-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="85c7f-108">*Les mises en page* résolvent ce problème.</span><span class="sxs-lookup"><span data-stu-id="85c7f-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="85c7f-109">Techniquement, une mise en page n’est qu’un autre composant.</span><span class="sxs-lookup"><span data-stu-id="85c7f-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="85c7f-110">Une mise en page est définie dans un modèle Razor ou dans le code C et peut utiliser [la liaison des données,](xref:blazor/data-binding) [l’injection de dépendance](xref:blazor/dependency-injection)et d’autres scénarios de composants.</span><span class="sxs-lookup"><span data-stu-id="85c7f-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="85c7f-111">Pour transformer un *composant* en *mise en page,* le composant :</span><span class="sxs-lookup"><span data-stu-id="85c7f-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="85c7f-112">Hérite `LayoutComponentBase`de , qui `Body` définit une propriété pour le contenu rendu à l’intérieur de la mise en page.</span><span class="sxs-lookup"><span data-stu-id="85c7f-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="85c7f-113">Utilise la syntaxe `@Body` Razor pour spécifier l’emplacement dans la mise en page où le contenu est rendu.</span><span class="sxs-lookup"><span data-stu-id="85c7f-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="85c7f-114">L’exemple de code suivant montre le modèle Razor d’un composant de mise en page, *MainLayout.razor*.</span><span class="sxs-lookup"><span data-stu-id="85c7f-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="85c7f-115">La disposition `LayoutComponentBase` hérite `@Body` et définit l’entre-deux la barre de navigation et le pied :</span><span class="sxs-lookup"><span data-stu-id="85c7f-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="85c7f-116">Dans une application basée Blazor sur l’un `MainLayout` des modèles d’applications, le composant (*MainLayout.razor*) est dans le dossier *partagé* de l’application.</span><span class="sxs-lookup"><span data-stu-id="85c7f-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="85c7f-117">Mise en page par défaut</span><span class="sxs-lookup"><span data-stu-id="85c7f-117">Default layout</span></span>

<span data-ttu-id="85c7f-118">Spécifiez la `Router` mise en page de l’application par défaut dans le fichier *App.razor* de l’application.</span><span class="sxs-lookup"><span data-stu-id="85c7f-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="85c7f-119">Le `Router` composant suivant, qui est Blazor fourni par les modèles `MainLayout` par défaut, définit la disposition par défaut du composant :</span><span class="sxs-lookup"><span data-stu-id="85c7f-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="85c7f-120">Pour fournir une `NotFound` mise en `LayoutView` page `NotFound` par défaut pour le contenu, spécifiez un contenu pour le contenu :</span><span class="sxs-lookup"><span data-stu-id="85c7f-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="85c7f-121">Pour plus d’informations sur le `Router` composant, voir <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="85c7f-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="85c7f-122">Spécifier la disposition comme une disposition par défaut dans le routeur est une pratique utile, car elle peut être remplacée par composant ou par dossier.</span><span class="sxs-lookup"><span data-stu-id="85c7f-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="85c7f-123">Préférez utiliser le routeur pour définir la mise en page par défaut de l’application parce que c’est la technique la plus générale.</span><span class="sxs-lookup"><span data-stu-id="85c7f-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="85c7f-124">Spécifier une mise en page dans un composant</span><span class="sxs-lookup"><span data-stu-id="85c7f-124">Specify a layout in a component</span></span>

<span data-ttu-id="85c7f-125">Utilisez la `@layout` directive Razor pour appliquer une disposition à un composant.</span><span class="sxs-lookup"><span data-stu-id="85c7f-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="85c7f-126">Le compilateur `@layout` se `LayoutAttribute`convertit en un , qui est appliqué à la classe de composants.</span><span class="sxs-lookup"><span data-stu-id="85c7f-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="85c7f-127">Le contenu du `MasterList` composant suivant est `MasterLayout` inséré dans `@Body`la position de :</span><span class="sxs-lookup"><span data-stu-id="85c7f-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="85c7f-128">Spécifier la disposition directement dans un composant remplace une `@layout` disposition par *défaut* définie dans le routeur ou une directive importée de *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="85c7f-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="85c7f-129">Sélection de mise en page centralisée</span><span class="sxs-lookup"><span data-stu-id="85c7f-129">Centralized layout selection</span></span>

<span data-ttu-id="85c7f-130">Chaque dossier d’une application peut en option contenir un fichier de modèle nommé *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="85c7f-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="85c7f-131">Le compilateur comprend les directives spécifiées dans le fichier des importations dans tous les modèles Razor dans le même dossier et récursive dans tous ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="85c7f-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="85c7f-132">Par conséquent, un fichier *_Imports.razor* contenant `@layout MyCoolLayout` s’assure que tous les `MyCoolLayout`composants d’un dossier utilisent .</span><span class="sxs-lookup"><span data-stu-id="85c7f-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="85c7f-133">Il n’est pas `@layout MyCoolLayout` nécessaire d’ajouter à plusieurs reprises à tous les fichiers *.razor* dans le dossier et les sous-plis.</span><span class="sxs-lookup"><span data-stu-id="85c7f-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="85c7f-134">`@using`directives sont également appliquées aux composants de la même manière.</span><span class="sxs-lookup"><span data-stu-id="85c7f-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="85c7f-135">Les importations suivantes de fichiers *_Imports.razor* :</span><span class="sxs-lookup"><span data-stu-id="85c7f-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="85c7f-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="85c7f-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="85c7f-137">Tous les composants Razor dans le même dossier et tous les sous-plieurs.</span><span class="sxs-lookup"><span data-stu-id="85c7f-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="85c7f-138">Espace de noms `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="85c7f-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="85c7f-139">Le fichier *_Imports.razor* est similaire au [fichier _ViewImports.cshtml pour les vues et les pages Razor,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux fichiers de composants Razor.</span><span class="sxs-lookup"><span data-stu-id="85c7f-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="85c7f-140">Spécifiant une mise en page dans *_Imports.razor* remplace une mise en page spécifiée comme *la disposition par défaut*du routeur .</span><span class="sxs-lookup"><span data-stu-id="85c7f-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="85c7f-141">Dispositions imbriquées</span><span class="sxs-lookup"><span data-stu-id="85c7f-141">Nested layouts</span></span>

<span data-ttu-id="85c7f-142">Les applications peuvent consister en des mises en page imbriquées.</span><span class="sxs-lookup"><span data-stu-id="85c7f-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="85c7f-143">Un composant peut faire référence à une mise en page qui, à son tour, fait référence à une autre mise en page.</span><span class="sxs-lookup"><span data-stu-id="85c7f-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="85c7f-144">Par exemple, les dispositions de nidification sont utilisées pour créer une structure de menu à plusieurs niveaux.</span><span class="sxs-lookup"><span data-stu-id="85c7f-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="85c7f-145">L’exemple suivant montre comment utiliser des mises en page imbriquées.</span><span class="sxs-lookup"><span data-stu-id="85c7f-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="85c7f-146">Le fichier *EpisodesComponent.razor* est le composant à afficher.</span><span class="sxs-lookup"><span data-stu-id="85c7f-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="85c7f-147">Le composant `MasterListLayout`fait référence à la :</span><span class="sxs-lookup"><span data-stu-id="85c7f-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="85c7f-148">Le fichier *MasterListLayout.razor* fournit le `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="85c7f-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="85c7f-149">La mise en `MasterLayout`page fait référence à une autre mise en page, , où il est rendu.</span><span class="sxs-lookup"><span data-stu-id="85c7f-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="85c7f-150">`EpisodesComponent`est rendu `@Body` où apparaît:</span><span class="sxs-lookup"><span data-stu-id="85c7f-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="85c7f-151">Enfin, `MasterLayout` dans *MasterLayout.razor* contient les éléments de mise en page de haut niveau, tels que l’en-tête, le menu principal et le pied.</span><span class="sxs-lookup"><span data-stu-id="85c7f-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="85c7f-152">`MasterListLayout`avec `EpisodesComponent` le est `@Body` rendu où apparaît:</span><span class="sxs-lookup"><span data-stu-id="85c7f-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="85c7f-153">Partagez une mise en page Razor Pages avec des composants intégrés</span><span class="sxs-lookup"><span data-stu-id="85c7f-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="85c7f-154">Lorsque des composants routables sont intégrés dans une application Razor Pages, la mise en page partagée de l’application peut être utilisée avec les composants.</span><span class="sxs-lookup"><span data-stu-id="85c7f-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="85c7f-155">Pour plus d’informations, consultez <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="85c7f-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85c7f-156">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="85c7f-156">Additional resources</span></span>

* <xref:mvc/views/layout>
