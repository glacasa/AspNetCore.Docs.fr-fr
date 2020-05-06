---
title: Composants Blazor ASP.net Core basés sur un modèle
author: guardrex
description: Découvrez comment les composants basés sur des modèles peuvent accepter un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: de603d3520c124b278312e5167a2f8bad14cf6e9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771065"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="ae93d-103">Composants Blazor ASP.net Core basés sur un modèle</span><span class="sxs-lookup"><span data-stu-id="ae93d-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="ae93d-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ae93d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ae93d-105">Les composants basés sur un modèle sont des composants qui acceptent un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="ae93d-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="ae93d-106">Les composants basés sur un modèle vous permettent de créer des composants de niveau supérieur qui sont plus réutilisables que les composants normaux.</span><span class="sxs-lookup"><span data-stu-id="ae93d-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="ae93d-107">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="ae93d-107">A couple of examples include:</span></span>

* <span data-ttu-id="ae93d-108">Composant de table qui permet à un utilisateur de spécifier des modèles pour l’en-tête, les lignes et le pied de page de la table.</span><span class="sxs-lookup"><span data-stu-id="ae93d-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="ae93d-109">Composant de liste qui permet à un utilisateur de spécifier un modèle pour afficher des éléments dans une liste.</span><span class="sxs-lookup"><span data-stu-id="ae93d-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="ae93d-110">Paramètres de modèle</span><span class="sxs-lookup"><span data-stu-id="ae93d-110">Template parameters</span></span>

<span data-ttu-id="ae93d-111">Un composant basé sur un modèle est défini en spécifiant un ou plusieurs paramètres `RenderFragment` de `RenderFragment<T>`composant de type ou.</span><span class="sxs-lookup"><span data-stu-id="ae93d-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="ae93d-112">Un fragment de rendu représente un segment de l’interface utilisateur à restituer.</span><span class="sxs-lookup"><span data-stu-id="ae93d-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="ae93d-113">`RenderFragment<T>`prend un paramètre de type qui peut être spécifié lors de l’appel du fragment de rendu.</span><span class="sxs-lookup"><span data-stu-id="ae93d-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="ae93d-114">`TableTemplate`-</span><span class="sxs-lookup"><span data-stu-id="ae93d-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="ae93d-115">Lorsque vous utilisez un composant basé sur un modèle, les paramètres de modèle peuvent être spécifiés à l’aide d’éléments enfants`TableHeader` qui `RowTemplate` correspondent aux noms des paramètres (et dans l’exemple suivant) :</span><span class="sxs-lookup"><span data-stu-id="ae93d-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="ae93d-116">Les contraintes de type générique seront prises en charge dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ae93d-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="ae93d-117">Pour plus d’informations, consultez [autoriser les contraintes de type générique (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="ae93d-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="ae93d-118">Paramètres de contexte de modèle</span><span class="sxs-lookup"><span data-stu-id="ae93d-118">Template context parameters</span></span>

<span data-ttu-id="ae93d-119">Les arguments de composant `RenderFragment<T>` de type passé comme éléments ont un paramètre `context` implicite nommé (par exemple, à partir `@context.PetId`de l’exemple de code précédent,), mais vous `Context` pouvez modifier le nom de paramètre à l’aide de l’attribut sur l’élément enfant.</span><span class="sxs-lookup"><span data-stu-id="ae93d-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="ae93d-120">Dans l’exemple suivant, l' `RowTemplate` attribut de `Context` l’élément spécifie le `pet` paramètre :</span><span class="sxs-lookup"><span data-stu-id="ae93d-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="ae93d-121">Vous pouvez également spécifier l' `Context` attribut sur l’élément de composant.</span><span class="sxs-lookup"><span data-stu-id="ae93d-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="ae93d-122">L’attribut `Context` spécifié s’applique à tous les paramètres de modèle spécifiés.</span><span class="sxs-lookup"><span data-stu-id="ae93d-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="ae93d-123">Cela peut être utile lorsque vous souhaitez spécifier le nom du paramètre de contenu pour le contenu enfant implicite (sans élément enfant d’encapsulation).</span><span class="sxs-lookup"><span data-stu-id="ae93d-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="ae93d-124">Dans l’exemple suivant, l' `Context` attribut apparaît sur l' `TableTemplate` élément et s’applique à tous les paramètres de modèle :</span><span class="sxs-lookup"><span data-stu-id="ae93d-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="ae93d-125">Composants génériques</span><span class="sxs-lookup"><span data-stu-id="ae93d-125">Generic-typed components</span></span>

<span data-ttu-id="ae93d-126">Les composants basés sur un modèle sont souvent typés de façon générique.</span><span class="sxs-lookup"><span data-stu-id="ae93d-126">Templated components are often generically typed.</span></span> <span data-ttu-id="ae93d-127">Par exemple, un composant `ListViewTemplate` générique peut être utilisé pour restituer `IEnumerable<T>` des valeurs.</span><span class="sxs-lookup"><span data-stu-id="ae93d-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="ae93d-128">Pour définir un composant générique, utilisez la [`@typeparam`](xref:mvc/views/razor#typeparam) directive pour spécifier les paramètres de type :</span><span class="sxs-lookup"><span data-stu-id="ae93d-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="ae93d-129">Lorsque vous utilisez des composants génériques, le paramètre de type est déduit si possible :</span><span class="sxs-lookup"><span data-stu-id="ae93d-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="ae93d-130">Sinon, le paramètre de type doit être spécifié explicitement à l’aide d’un attribut qui correspond au nom du paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="ae93d-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="ae93d-131">Dans l’exemple suivant, `TItem="Pet"` spécifie le type :</span><span class="sxs-lookup"><span data-stu-id="ae93d-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
