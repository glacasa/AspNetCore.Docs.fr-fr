---
titre : 'ASP.NET Core Blazor dispositions’auteur : Description : 'Apprenez à créer des composants de disposition réutilisables pour les Blazor applications. '
monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---
# <a name="aspnet-core-blazor-layouts"></a>Dispositions de ASP.NET Core Blazor

Par [Rainer Stropek](https://www.timecockpit.com) et [Luke Latham](https://github.com/guardrex)

Certains éléments de l’application, tels que les menus, les messages de copyright et les logos de l’entreprise, font généralement partie de la mise en page globale de l’application et sont utilisés par chaque composant de l’application. La copie du code de ces éléments dans tous les composants d’une application n’est pas une approche efficace. Chaque fois que l’un des éléments requiert une mise à jour, chaque composant doit être mis à jour. Une telle duplication est difficile à gérer et peut entraîner une incohérence du contenu au fil du temps. Les *dispositions* résolvent ce problème.

Techniquement, une disposition est simplement un autre composant. Une disposition est définie dans un Razor modèle ou dans du code C# et peut utiliser la [liaison de données](xref:blazor/data-binding), l' [injection de dépendances](xref:blazor/dependency-injection)et d’autres scénarios de composants.

Pour transformer un *composant* en une *disposition*, le composant :

* Hérite de <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , qui définit une <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> propriété pour le contenu rendu à l’intérieur de la disposition.
* Utilise la Razor syntaxe `@Body` pour spécifier l’emplacement dans la balise de mise en page où le contenu est restitué.

L’exemple de code suivant montre le Razor modèle d’un composant de disposition, *MainLayout. Razor*. La disposition hérite <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> de et définit la `@Body` entre la barre de navigation et le pied de page :

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Dans une application basée sur l’un des Blazor modèles d’application, le `MainLayout` composant (*MainLayout. Razor*) se trouve dans le dossier *partagé* de l’application.

## <a name="default-layout"></a>Disposition par défaut

Spécifiez la disposition de l’application par défaut dans le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant dans le fichier *app. Razor* de l’application. Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant suivant, qui est fourni par les modèles par défaut Blazor , définit la disposition par défaut sur le `MainLayout` composant :

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Pour fournir une disposition par défaut pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu, spécifiez un <xref:Microsoft.AspNetCore.Components.LayoutView> pour le <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> contenu :

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Pour plus d’informations sur le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant, consultez <xref:blazor/routing> .

La spécification de la disposition comme disposition par défaut dans le routeur est une pratique utile, car elle peut être remplacée par composant ou par dossier. Préférez utiliser le routeur pour définir la disposition par défaut de l’application, car il s’agit de la technique la plus générale.

## <a name="specify-a-layout-in-a-component"></a>Spécifier une disposition dans un composant

Utilisez la Razor directive `@layout` pour appliquer une disposition à un composant. Le compilateur convertit `@layout` en <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , qui est appliqué à la classe de composant.

Le contenu du composant suivant `MasterList` est inséré dans le `MasterLayout` à la position de `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

La spécification de la disposition directement dans un composant remplace un ensemble de *dispositions par défaut* dans le routeur ou une `@layout` directive importée à partir de *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Sélection de la disposition centralisée

Chaque dossier d’une application peut éventuellement contenir un fichier modèle nommé *_Imports. Razor*. Le compilateur comprend les directives spécifiées dans le fichier Imports dans tous les Razor modèles du même dossier et de manière récursive dans tous ses sous-dossiers. Par conséquent, un fichier *_Imports. Razor* contenant `@layout MyCoolLayout` s’assure que tous les composants d’un dossier utilisent `MyCoolLayout` . Il n’est pas nécessaire d’ajouter à plusieurs reprises `@layout MyCoolLayout` à tous les fichiers *. Razor* dans le dossier et les sous-dossiers. `@using`les directives sont également appliquées aux composants de la même façon.

Le fichier *_Imports. Razor* suivant importe les éléments suivants :

* `MyCoolLayout`.
* Tous les Razor composants du même dossier et de tous les sous-dossiers.
* Espace de noms `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Le fichier *_Imports. Razor* est semblable au [fichier _ViewImports. cshtml pour les Razor affichages et les pages,](xref:mvc/views/layout#importing-shared-directives) mais appliqué spécifiquement aux Razor fichiers de composants.

La spécification d’une disposition dans *_Imports. Razor* remplace une disposition spécifiée comme *disposition par défaut*du routeur.

## <a name="nested-layouts"></a>Dispositions imbriquées

Les applications peuvent se composer de dispositions imbriquées. Un composant peut faire référence à une disposition qui, à son tour, fait référence à une autre disposition. Par exemple, les dispositions d’imbrication sont utilisées pour créer une structure de menus à plusieurs niveaux.

L’exemple suivant montre comment utiliser des dispositions imbriquées. Le fichier *EpisodesComponent. Razor* est le composant à afficher. Le composant fait référence à `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Le fichier *MasterListLayout. Razor* fournit le `MasterListLayout` . La disposition fait référence à une autre disposition, `MasterLayout` , où elle est rendue. `EpisodesComponent`l’emplacement est affiché `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Enfin, `MasterLayout` dans *MasterLayout. Razor* contient les éléments de disposition de niveau supérieur, tels que l’en-tête, le menu principal et le pied de page. `MasterListLayout`avec `EpisodesComponent` est rendu où `@Body` s’affiche :

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Partager une Razor disposition de pages avec des composants intégrés

Lorsque des composants routables sont intégrés à une Razor application pages, la disposition partagée de l’application peut être utilisée avec les composants. Pour plus d'informations, consultez <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/layout>
