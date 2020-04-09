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
# <a name="aspnet-core-opno-locblazor-templated-components"></a>ASP.NET composants Blazor modélés de base

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Les composants modélés sont des composants qui acceptent un ou plusieurs modèles d’interface utilisateur comme paramètres, qui peuvent ensuite être utilisés dans le cadre de la logique de rendu du composant. Les composants modélés vous permettent d’écrire des composants de plus haut niveau qui sont plus réutilisables que les composants réguliers. Voici quelques exemples :

* Un composant de table qui permet à un utilisateur de spécifier des modèles pour l’en-tête, les rangées et le pied de la table.
* Un composant de liste qui permet à un utilisateur de spécifier un modèle pour le rendu des éléments dans une liste.

## <a name="template-parameters"></a>Paramètres de modèle

Un composant modélisant est défini en spécifiant un ou plusieurs paramètres de composant de type `RenderFragment` ou `RenderFragment<T>`. Un fragment de rendu représente un segment de l’interface utilisateur à rendre. `RenderFragment<T>`prend un paramètre de type qui peut être spécifié lorsque le fragment de rendu est invoqué.

`TableTemplate`Composant:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Lors de l’utilisation d’un composant modélé, les paramètres du modèle`TableHeader` `RowTemplate` peuvent être spécifiés à l’aide d’éléments pour enfants qui correspondent aux noms des paramètres (et dans l’exemple suivant) :

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
> Les contraintes de type générique seront prises en charge dans une version future. Pour plus d’informations, voir [Autoriser les contraintes de type générique (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Paramètres de contexte de modèle

Les arguments `RenderFragment<T>` de composants de type `context` adoptés en tant qu’éléments ont un paramètre implicite nommé (par exemple à partir de l’échantillon de code précédent, `@context.PetId`), mais vous pouvez changer le nom du paramètre en utilisant l’attribut `Context` sur l’élément enfant. Dans l’exemple `RowTemplate` suivant, `Context` l’attribut de `pet` l’élément spécifie le paramètre :

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

Alternativement, vous pouvez `Context` spécifier l’attribut sur l’élément composant. L’attribut spécifié `Context` s’applique à tous les paramètres spécifiés du modèle. Cela peut être utile lorsque vous souhaitez spécifier le nom du paramètre de contenu pour le contenu implicite de l’enfant (sans aucun élément d’enfant d’emballage). Dans l’exemple `Context` suivant, l’attribut apparaît sur l’élément `TableTemplate` et s’applique à tous les paramètres du modèle :

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

## <a name="generic-typed-components"></a>Composants de type générique

Les composants modélés sont souvent dactylographes génériquement. Par exemple, `ListViewTemplate` un composant générique `IEnumerable<T>` peut être utilisé pour rendre les valeurs. Pour définir un composant [`@typeparam`](xref:mvc/views/razor#typeparam) générique, utilisez la directive pour spécifier les paramètres de type :

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Lors de l’utilisation de composants de type générique, le paramètre de type est déduit si possible :

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Dans le cas contraire, le paramètre de type doit être spécifié explicitement à l’aide d’un attribut qui correspond au nom du paramètre de type. Dans l’exemple `TItem="Pet"` suivant, spécifie le type :

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
