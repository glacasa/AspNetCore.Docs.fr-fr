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
# <a name="aspnet-core-blazor-templated-components"></a>Composants Blazor ASP.net Core basés sur un modèle

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Les composants basés sur un modèle sont des composants qui acceptent un ou plusieurs modèles d’interface utilisateur en tant que paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant. Les composants basés sur un modèle vous permettent de créer des composants de niveau supérieur qui sont plus réutilisables que les composants normaux. Voici quelques exemples :

* Composant de table qui permet à un utilisateur de spécifier des modèles pour l’en-tête, les lignes et le pied de page de la table.
* Composant de liste qui permet à un utilisateur de spécifier un modèle pour afficher des éléments dans une liste.

## <a name="template-parameters"></a>Paramètres de modèle

Un composant basé sur un modèle est défini en spécifiant un ou plusieurs paramètres `RenderFragment` de `RenderFragment<T>`composant de type ou. Un fragment de rendu représente un segment de l’interface utilisateur à restituer. `RenderFragment<T>`prend un paramètre de type qui peut être spécifié lors de l’appel du fragment de rendu.

`TableTemplate`-

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Lorsque vous utilisez un composant basé sur un modèle, les paramètres de modèle peuvent être spécifiés à l’aide d’éléments enfants`TableHeader` qui `RowTemplate` correspondent aux noms des paramètres (et dans l’exemple suivant) :

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
> Les contraintes de type générique seront prises en charge dans une version ultérieure. Pour plus d’informations, consultez [autoriser les contraintes de type générique (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Paramètres de contexte de modèle

Les arguments de composant `RenderFragment<T>` de type passé comme éléments ont un paramètre `context` implicite nommé (par exemple, à partir `@context.PetId`de l’exemple de code précédent,), mais vous `Context` pouvez modifier le nom de paramètre à l’aide de l’attribut sur l’élément enfant. Dans l’exemple suivant, l' `RowTemplate` attribut de `Context` l’élément spécifie le `pet` paramètre :

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

Vous pouvez également spécifier l' `Context` attribut sur l’élément de composant. L’attribut `Context` spécifié s’applique à tous les paramètres de modèle spécifiés. Cela peut être utile lorsque vous souhaitez spécifier le nom du paramètre de contenu pour le contenu enfant implicite (sans élément enfant d’encapsulation). Dans l’exemple suivant, l' `Context` attribut apparaît sur l' `TableTemplate` élément et s’applique à tous les paramètres de modèle :

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

## <a name="generic-typed-components"></a>Composants génériques

Les composants basés sur un modèle sont souvent typés de façon générique. Par exemple, un composant `ListViewTemplate` générique peut être utilisé pour restituer `IEnumerable<T>` des valeurs. Pour définir un composant générique, utilisez la [`@typeparam`](xref:mvc/views/razor#typeparam) directive pour spécifier les paramètres de type :

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Lorsque vous utilisez des composants génériques, le paramètre de type est déduit si possible :

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Sinon, le paramètre de type doit être spécifié explicitement à l’aide d’un attribut qui correspond au nom du paramètre de type. Dans l’exemple suivant, `TItem="Pet"` spécifie le type :

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
