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
# <a name="aspnet-core-opno-locblazor-layouts"></a>ASP.NET mises en Blazor page De base

Par [Rainer Stropek](https://www.timecockpit.com) et [Luke Latham](https://github.com/guardrex)

Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la mise en page globale de l’application et sont utilisés par chaque composant de l’application. Copier le code de ces éléments dans tous les composants d’une application n’est pas une approche&mdash;efficace chaque fois que l’un des éléments nécessite une mise à jour, chaque composant doit être mis à jour. Une telle duplication est difficile à maintenir et peut conduire à un contenu incohérent au fil du temps. *Les mises en page* résolvent ce problème.

Techniquement, une mise en page n’est qu’un autre composant. Une mise en page est définie dans un modèle Razor ou dans le code C et peut utiliser [la liaison des données,](xref:blazor/data-binding) [l’injection de dépendance](xref:blazor/dependency-injection)et d’autres scénarios de composants.

Pour transformer un *composant* en *mise en page,* le composant :

* Hérite `LayoutComponentBase`de , qui `Body` définit une propriété pour le contenu rendu à l’intérieur de la mise en page.
* Utilise la syntaxe `@Body` Razor pour spécifier l’emplacement dans la mise en page où le contenu est rendu.

L’exemple de code suivant montre le modèle Razor d’un composant de mise en page, *MainLayout.razor*. La disposition `LayoutComponentBase` hérite `@Body` et définit l’entre-deux la barre de navigation et le pied :

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Dans une application basée Blazor sur l’un `MainLayout` des modèles d’applications, le composant (*MainLayout.razor*) est dans le dossier *partagé* de l’application.

## <a name="default-layout"></a>Mise en page par défaut

Spécifiez la `Router` mise en page de l’application par défaut dans le fichier *App.razor* de l’application. Le `Router` composant suivant, qui est Blazor fourni par les modèles `MainLayout` par défaut, définit la disposition par défaut du composant :

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Pour fournir une `NotFound` mise en `LayoutView` page `NotFound` par défaut pour le contenu, spécifiez un contenu pour le contenu :

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Pour plus d’informations sur le `Router` composant, voir <xref:blazor/routing>.

Spécifier la disposition comme une disposition par défaut dans le routeur est une pratique utile, car elle peut être remplacée par composant ou par dossier. Préférez utiliser le routeur pour définir la mise en page par défaut de l’application parce que c’est la technique la plus générale.

## <a name="specify-a-layout-in-a-component"></a>Spécifier une mise en page dans un composant

Utilisez la `@layout` directive Razor pour appliquer une disposition à un composant. Le compilateur `@layout` se `LayoutAttribute`convertit en un , qui est appliqué à la classe de composants.

Le contenu du `MasterList` composant suivant est `MasterLayout` inséré dans `@Body`la position de :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Spécifier la disposition directement dans un composant remplace une `@layout` disposition par *défaut* définie dans le routeur ou une directive importée de *_Imports.razor*.

## <a name="centralized-layout-selection"></a>Sélection de mise en page centralisée

Chaque dossier d’une application peut en option contenir un fichier de modèle nommé *_Imports.razor*. Le compilateur comprend les directives spécifiées dans le fichier des importations dans tous les modèles Razor dans le même dossier et récursive dans tous ses sous-dossiers. Par conséquent, un fichier *_Imports.razor* contenant `@layout MyCoolLayout` s’assure que tous les `MyCoolLayout`composants d’un dossier utilisent . Il n’est pas `@layout MyCoolLayout` nécessaire d’ajouter à plusieurs reprises à tous les fichiers *.razor* dans le dossier et les sous-plis. `@using`directives sont également appliquées aux composants de la même manière.

Les importations suivantes de fichiers *_Imports.razor* :

* `MyCoolLayout`.
* Tous les composants Razor dans le même dossier et tous les sous-plieurs.
* Espace de noms `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Le fichier *_Imports.razor* est similaire au [fichier _ViewImports.cshtml pour les vues et les pages Razor,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux fichiers de composants Razor.

Spécifiant une mise en page dans *_Imports.razor* remplace une mise en page spécifiée comme *la disposition par défaut*du routeur .

## <a name="nested-layouts"></a>Dispositions imbriquées

Les applications peuvent consister en des mises en page imbriquées. Un composant peut faire référence à une mise en page qui, à son tour, fait référence à une autre mise en page. Par exemple, les dispositions de nidification sont utilisées pour créer une structure de menu à plusieurs niveaux.

L’exemple suivant montre comment utiliser des mises en page imbriquées. Le fichier *EpisodesComponent.razor* est le composant à afficher. Le composant `MasterListLayout`fait référence à la :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Le fichier *MasterListLayout.razor* fournit le `MasterListLayout`. La mise en `MasterLayout`page fait référence à une autre mise en page, , où il est rendu. `EpisodesComponent`est rendu `@Body` où apparaît:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Enfin, `MasterLayout` dans *MasterLayout.razor* contient les éléments de mise en page de haut niveau, tels que l’en-tête, le menu principal et le pied. `MasterListLayout`avec `EpisodesComponent` le est `@Body` rendu où apparaît:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Partagez une mise en page Razor Pages avec des composants intégrés

Lorsque des composants routables sont intégrés dans une application Razor Pages, la mise en page partagée de l’application peut être utilisée avec les composants. Pour plus d’informations, consultez <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/layout>
