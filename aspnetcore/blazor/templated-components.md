---
title: ASP.NET composants Blazor modélés de base
author: guardrex
description: Découvrez comment les composants modélés peuvent accepter un ou plusieurs modèles d’interface utilisateur comme paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989501"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="e95d9-103">ASP.NET composants Blazor modélés de base</span><span class="sxs-lookup"><span data-stu-id="e95d9-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="e95d9-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e95d9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e95d9-105">Les composants modélés sont des composants qui acceptent un ou plusieurs modèles d’interface utilisateur comme paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="e95d9-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="e95d9-106">Les composants modélés vous permettent d’écrire des composants de plus haut niveau qui sont plus réutilisables que les composants réguliers.</span><span class="sxs-lookup"><span data-stu-id="e95d9-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="e95d9-107">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="e95d9-107">A couple of examples include:</span></span>

* <span data-ttu-id="e95d9-108">Un composant de table qui permet à un utilisateur de spécifier des modèles pour l’en-tête, les rangées et le pied de la table.</span><span class="sxs-lookup"><span data-stu-id="e95d9-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="e95d9-109">Un composant de liste qui permet à un utilisateur de spécifier un modèle pour le rendu des éléments dans une liste.</span><span class="sxs-lookup"><span data-stu-id="e95d9-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="e95d9-110">Paramètres de modèle</span><span class="sxs-lookup"><span data-stu-id="e95d9-110">Template parameters</span></span>

<span data-ttu-id="e95d9-111">Un composant modélisant est défini en spécifiant un ou plusieurs paramètres de composant de type `RenderFragment` ou `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="e95d9-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="e95d9-112">Un fragment de rendu représente un segment de l’interface utilisateur à rendre.</span><span class="sxs-lookup"><span data-stu-id="e95d9-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="e95d9-113">`RenderFragment<T>`prend un paramètre de type qui peut être spécifié lorsque le fragment de rendu est invoqué.</span><span class="sxs-lookup"><span data-stu-id="e95d9-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="e95d9-114">`TableTemplate`Composant:</span><span class="sxs-lookup"><span data-stu-id="e95d9-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="e95d9-115">Lors de l’utilisation d’un composant modélé, les paramètres du modèle`TableHeader` `RowTemplate` peuvent être spécifiés à l’aide d’éléments pour enfants qui correspondent aux noms des paramètres (et dans l’exemple suivant) :</span><span class="sxs-lookup"><span data-stu-id="e95d9-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="e95d9-116">Les contraintes de type générique seront prises en charge dans une version future.</span><span class="sxs-lookup"><span data-stu-id="e95d9-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="e95d9-117">Pour plus d’informations, voir [Autoriser les contraintes de type générique (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="e95d9-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="e95d9-118">Paramètres de contexte de modèle</span><span class="sxs-lookup"><span data-stu-id="e95d9-118">Template context parameters</span></span>

<span data-ttu-id="e95d9-119">Les arguments `RenderFragment<T>` de composants de type `context` adoptés en tant qu’éléments ont un paramètre implicite nommé (par exemple à partir de l’échantillon de code précédent, `@context.PetId`), mais vous pouvez changer le nom du paramètre en utilisant l’attribut `Context` sur l’élément enfant.</span><span class="sxs-lookup"><span data-stu-id="e95d9-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="e95d9-120">Dans l’exemple `RowTemplate` suivant, `Context` l’attribut de `pet` l’élément spécifie le paramètre :</span><span class="sxs-lookup"><span data-stu-id="e95d9-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="e95d9-121">Alternativement, vous pouvez `Context` spécifier l’attribut sur l’élément composant.</span><span class="sxs-lookup"><span data-stu-id="e95d9-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="e95d9-122">L’attribut spécifié `Context` s’applique à tous les paramètres spécifiés du modèle.</span><span class="sxs-lookup"><span data-stu-id="e95d9-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="e95d9-123">Cela peut être utile lorsque vous souhaitez spécifier le nom du paramètre de contenu pour le contenu implicite de l’enfant (sans aucun élément d’enfant d’emballage).</span><span class="sxs-lookup"><span data-stu-id="e95d9-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="e95d9-124">Dans l’exemple `Context` suivant, l’attribut apparaît sur l’élément `TableTemplate` et s’applique à tous les paramètres du modèle :</span><span class="sxs-lookup"><span data-stu-id="e95d9-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="e95d9-125">Composants de type générique</span><span class="sxs-lookup"><span data-stu-id="e95d9-125">Generic-typed components</span></span>

<span data-ttu-id="e95d9-126">Les composants modélés sont souvent dactylographes génériquement.</span><span class="sxs-lookup"><span data-stu-id="e95d9-126">Templated components are often generically typed.</span></span> <span data-ttu-id="e95d9-127">Par exemple, `ListViewTemplate` un composant générique `IEnumerable<T>` peut être utilisé pour rendre les valeurs.</span><span class="sxs-lookup"><span data-stu-id="e95d9-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="e95d9-128">Pour définir un composant [`@typeparam`](xref:mvc/views/razor#typeparam) générique, utilisez la directive pour spécifier les paramètres de type :</span><span class="sxs-lookup"><span data-stu-id="e95d9-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="e95d9-129">Lors de l’utilisation de composants de type générique, le paramètre de type est déduit si possible :</span><span class="sxs-lookup"><span data-stu-id="e95d9-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="e95d9-130">Dans le cas contraire, le paramètre de type doit être spécifié explicitement à l’aide d’un attribut qui correspond au nom du paramètre de type.</span><span class="sxs-lookup"><span data-stu-id="e95d9-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="e95d9-131">Dans l’exemple `TItem="Pet"` suivant, spécifie le type :</span><span class="sxs-lookup"><span data-stu-id="e95d9-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
