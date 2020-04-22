---
title: Créer et utiliser ASP.NET composants Core Razor
author: guardrex
description: Apprenez à créer et à utiliser des composants Razor, y compris comment se lier aux données, gérer les événements et gérer les cycles de vie des composants.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791489"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Créer et utiliser ASP.NET composants Core Razor

Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), et Tobias [Bartsch](https://www.aveo-solutions.com/)

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

Blazorapplications sont construites à l’aide *de composants*. Un composant est un morceau autonome de l’interface utilisateur (UI), comme une page, un dialogue ou une forme. Un composant comprend la balisage HTML et la logique de traitement nécessaire pour injecter des données ou répondre aux événements de l’interface utilisateur. Les composants sont flexibles et légers. Ils peuvent être imbriqués, réutilisés et partagés entre les projets.

## <a name="component-classes"></a>Classes de composants

Les composants sont implémentés dans les fichiers de composants [Razor](xref:mvc/views/razor) (*.razor*) à l’aide d’une combinaison de balisage C et HTML. Un composant Blazor est officiellement appelé un *composant Razor*.

Le nom d’un composant doit commencer par un caractère majuscule. Par exemple, *MyCoolComponent.razor* est valide, et *myCoolComponent.razor* est invalide.

L’interface utilisateur pour un composant est définie à l’aide de HTML. La logique de rendu dynamique (par exemple, les boucles, conditions, expressions) est ajoutée à l’aide d’une syntaxe C# intégrée appelée [Razor](xref:mvc/views/razor). Lorsqu’une application est compilée, la logique de balisage HTML et de rendu C est convertie en classe de composants. Le nom de la classe générée correspond au nom du fichier.

Les membres de la classe de composants sont définis dans un bloc `@code`. Dans `@code` le bloc, l’état des composants (propriétés, champs) est spécifié avec des méthodes de manipulation des événements ou pour définir d’autres logiques de composants. Plus d’un bloc `@code` est autorisé.

Les membres de composant peuvent être employés dans le cadre de `@`la logique de rendu du composant en utilisant des expressions de C 'qui commencent par . Par exemple, un champ C est rendu `@` en préfixant le nom du champ. L’exemple suivant évalue et rend :

* `_headingFontStyle`à la valeur de `font-style`propriété CSS pour .
* `_headingText`au contenu de `<h1>` l’élément.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Une fois le composant rendu au départ, le composant régénére son arbre de rendu en réponse aux événements. Blazorcompare ensuite le nouvel arbre de rendu par rapport au précédent et applique toutes les modifications au modèle d’objet document (DOM) du navigateur.

Les composants sont des classes ordinaires de C et peuvent être placés n’importe où dans un projet. Les composants qui produisent des pages Web résident généralement dans le dossier *Pages.* Les composants non-pages sont fréquemment placés dans le dossier *partagé* ou dans un dossier personnalisé ajouté au projet.

En règle générale, l’espace de nom d’un composant est dérivé de l’espace de nom racine de l’application et de l’emplacement (dossier) du composant dans l’application. Si l’espace de nom `BlazorApp` de `Counter` racine de l’application est et le composant réside dans le dossier *Pages* :

* L’espace `Counter` de nom `BlazorApp.Pages`du composant est .
* Le nom de type entièrement `BlazorApp.Pages.Counter`qualifié du composant est .

Pour plus d’informations, consultez la section [composants d’importation.](#import-components)

Pour utiliser un dossier personnalisé, ajoutez l’espace de nom du dossier personnalisé au composant parent ou au fichier *_Imports.razor* de l’application. Par exemple, l’espace de nom suivant met les composants dans un dossier `BlazorApp`de composants *disponibles* lorsque l’espace de nom de racine de l’application est :

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Les ressources statiques

Blazorsuit la convention de ASP.NET applications Core plaçant des actifs statiques sous le [dossier web root (wwwroot)](xref:fundamentals/index#web-root)du projet.

Utilisez un chemin de`/`base-relatif ( ) pour se référer à la racine web pour un actif statique. Dans l’exemple suivant, *logo.png* est physiquement situé dans le dossier *'PROJECT ROOT'/wwwroot/images:*

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Les composants de rasoir **ne** prennent`~/`pas en charge la notation tilde-slash ( ).

Pour plus d’informations sur la <xref:host-and-deploy/blazor/index#app-base-path>définition du chemin de base d’une application, voir .

## <a name="tag-helpers-arent-supported-in-components"></a>Tag Helpers ne sont pas pris en charge dans les composants

[Tag Helpers](xref:mvc/views/tag-helpers/intro) ne sont pas pris en charge dans les composants Razor *(fichiers .razor).* Pour fournir des fonctionnalités de Blazortype Tag Helper, créez un composant avec les mêmes fonctionnalités que l’aide Tag et utilisez le composant à la place.

## <a name="use-components"></a>Utiliser des composants

Les composants peuvent inclure d’autres composants en les déclarant à l’aide de la syntaxe d’éléments HTML. Le balisage pour l’utilisation d’un composant ressemble à une balise HTML où le nom de la balise est le type du composant.

La majoration suivante dans *Index.razor* rend un `HeadingComponent` cas :

```razor
<HeadingComponent />
```

*Composants/HeadingComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Si un composant contient un élément HTML avec une première lettre majuscule qui ne correspond pas à un nom de composant, un avertissement est émis indiquant que l’élément a un nom inattendu. L’ajout d’une `@using` directive pour l’espace nom du composant rend le composant disponible, ce qui résout l’avertissement.

## <a name="routing"></a>Routage

L’itinéraire Blazor est réalisé en fournissant un modèle d’itinéraire à chaque composant accessible de l’application.

Lorsqu’un fichier `@page` Razor avec directive est compilé, <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> la classe générée reçoit un modèle d’itinéraire précisant. Au moment de l’exécution, le `RouteAttribute` routeur recherche des classes de composants avec un composant et rend n’importe quel composant a un modèle d’itinéraire qui correspond à l’URL demandée.

```razor
@page "/ParentComponent"

...
```

Pour plus d’informations, consultez <xref:blazor/routing>.

## <a name="parameters"></a>Paramètres

### <a name="route-parameters"></a>Paramètres d’itinéraire

Les composants peuvent recevoir les paramètres d’itinéraire à partir du modèle d’itinéraire fourni dans la `@page` directive. Le routeur utilise les paramètres d’itinéraire pour remplir les paramètres de composant correspondants.

*Pages/RouteParameter.razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Les paramètres facultatifs ne `@page` sont pas pris en charge, de sorte que deux directives sont appliquées dans l’exemple précédent. Le premier permet la navigation sur le composant sans paramètre. La `@page` deuxième directive `{text}` reçoit le paramètre d’itinéraire et attribue la valeur à la `Text` propriété.

La syntaxe de`*`/`**`paramètre *fourre-tout* ( qui capture le chemin à travers plusieurs limites de pliage, **n’est pas** prise en charge dans les composants Razor (*.razor*).

### <a name="component-parameters"></a>Paramètres de composant

Les composants peuvent avoir des paramètres de composant , qui `[Parameter]` sont *définis*en utilisant des propriétés publiques sur la classe de composant avec l’attribut. Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.

*Composants/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Dans l’exemple suivant de `ParentComponent` l’application d’échantillon, la valeur de la `Title` propriété de la `ChildComponent`.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Ne créez pas de composants qui écrivent à leurs propres *paramètres de composant,* utilisez plutôt un champ privé. Pour plus d’informations, voir le [Don’t create components qui écrivent à leur propre section propriétés de paramètres.](#dont-create-components-that-write-to-their-own-parameter-properties)

## <a name="child-content"></a>Contenu pour enfants

Les composants peuvent définir le contenu d’un autre composant. Le composant d’attribution fournit le contenu entre les balises qui spécifient le composant de réception.

Dans l’exemple `ChildComponent` suivant, `ChildContent` le a `RenderFragment`une propriété qui représente un , qui représente un segment de l’interface utilisateur à rendre. La valeur `ChildContent` de est positionnée dans le balisage du composant où le contenu doit être rendu. La valeur `ChildContent` de est reçue de la composante mère et `panel-body`rendue à l’intérieur du panneau Bootstrap.

*Composants/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> La propriété `RenderFragment` qui reçoit `ChildContent` le contenu doit être nommée par convention.

L’application `ParentComponent` de l’échantillon peut `ChildComponent` fournir du contenu `<ChildComponent>` pour rendre le contenu en plaçant le contenu à l’intérieur des balises.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attribuer des éclaboussures et des paramètres arbitraires

Les composants peuvent capturer et rendre des attributs supplémentaires en plus des paramètres déclarés du composant. D’autres attributs peuvent être capturés dans un dictionnaire, puis *éclaboussés* sur un élément lorsque le composant est rendu à l’aide de la [`@attributes`](xref:mvc/views/razor#attributes) directive Razor. Ce scénario est utile lors de la définition d’un composant qui produit un élément de balisage qui prend en charge une variété de personnalisations. Par exemple, il peut être fastidieux de `<input>` définir les attributs séparément pour un qui prend en charge de nombreux paramètres.

Dans l’exemple suivant, `<input>` `id="useIndividualParams"`le premier élément ( ) `<input>` utilise`id="useAttributesDict"`des paramètres de composant individuels, tandis que le deuxième élément ( ) utilise l’éclaboussure d’attribut :

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

Le type de paramètre doit être implémenté `IEnumerable<KeyValuePair<string, object>>` avec des touches de chaîne. L’utilisation `IReadOnlyDictionary<string, object>` est également une option dans ce scénario.

Les éléments `<input>` rendus à l’aide des deux approches sont identiques :

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

Pour accepter des attributs arbitraires, `[Parameter]` définissez `CaptureUnmatchedValues` un paramètre de composant à l’aide de l’attribut avec la propriété définie à `true`:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

La `CaptureUnmatchedValues` propriété `[Parameter]` sur permet au paramètre de correspondre à tous les attributs qui ne correspondent à aucun autre paramètre. Un composant ne peut définir `CaptureUnmatchedValues`un seul paramètre avec . Le type de `CaptureUnmatchedValues` propriété utilisé `Dictionary<string, object>` avec doit être assignable à partir de clés de chaîne. `IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` sont également des options dans ce scénario.

La position `@attributes` des attributs relatifs à la position des attributs d’élément est importante. Lorsque `@attributes` sont éclaboussés sur l’élément, les attributs sont traités de droite à gauche (du dernier au premier). Prenons l’exemple suivant d’un `Child` composant qui consomme un composant :

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

L’attribut `Child` `extra` du composant est réglé `@attributes`au droit de . Le `Parent` composant rendu `<div>` contient `extra="5"` lorsqu’il est passé par l’attribut supplémentaire parce que les attributs sont traités de droite à gauche (dernier à d’abord):

```html
<div extra="5" />
```

Dans l’exemple suivant, `extra` `@attributes` l’ordre et `Child` l’inverse dans le composant `<div>`:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Le rendu `<div>` dans `Parent` le `extra="10"` composant contient une fois passé par l’attribut supplémentaire:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Capturez les références aux composants

Les références de composants fournissent un moyen de référencer une instance `Show` de `Reset`composant afin que vous puissiez émettre des commandes à cette instance, telle que ou . Pour saisir une référence de composant :

* Ajoutez [`@ref`](xref:mvc/views/razor#ref) un attribut à la composante enfant.
* Définissez un champ avec le même type que la composante enfant.

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

Lorsque le composant est `_loginDialog` rendu, le champ `MyLoginDialog` est peuplé avec l’instance de composant de l’enfant. Vous pouvez alors invoquer des méthodes .NET sur l’instance du composant.

> [!IMPORTANT]
> La `_loginDialog` variable n’est peuplée qu’après la remise `MyLoginDialog` du composant et sa sortie comprend l’élément. Jusque-là, il n’y a rien à faire référence. Pour manipuler les références de composants après que le composant a terminé le rendu, utilisez les [méthodes OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).

Pour référencer les composants d’une boucle, voir [les références Capture à plusieurs composants similaires pour enfants (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Bien que la capture des références de composants utilise une syntaxe similaire à [la capture des références d’éléments,](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)ce n’est pas une fonction d’interop JavaScript. Les références de composants ne&mdash;sont pas passées au code JavaScript qu’elles ne sont utilisées que dans le code .NET.

> [!NOTE]
> N’utilisez **pas** de références de composants pour muter l’état des composants pour enfants. Utilisez plutôt des paramètres déclaratifs normaux pour transmettre des données aux composants de l’enfant. L’utilisation de paramètres déclaratifs normaux entraîne des composants pour enfants qui rerender aux bons moments automatiquement.

## <a name="invoke-component-methods-externally-to-update-state"></a>Invoquer des méthodes de composants à l’extérieur pour mettre à jour l’état

Blazorutilise `SynchronizationContext` un pour faire respecter un seul fil logique de l’exécution. Les méthodes du cycle de [vie d’un](xref:blazor/lifecycle) composant Blazor et les `SynchronizationContext`rappels d’événements qui sont soulevés par sont exécutés à ce sujet . Dans le cas où un composant doit être mis à jour en fonction `InvokeAsync` d’un événement Blazorexterne, comme une minuterie ou d’autres notifications, utiliser la méthode, qui sera expédiée à 's `SynchronizationContext`.

Par exemple, envisagez un *service de notificateur* qui peut aviser tout élément d’écoute de l’état mis à jour :

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

Enregistrez-vous `NotifierService` comme une seuletion:

* Dans Blazor WebAssembly, inscrivez `Program.Main`le service dans :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Dans Blazor Server, enregistrez `Startup.ConfigureServices`le service dans :

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Utilisez `NotifierService` le pour mettre à jour un composant:

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

Dans l’exemple `NotifierService` précédent, invoque la méthode du `OnNotify` composant en dehors de Blazor's `SynchronizationContext`. `InvokeAsync`est utilisé pour passer au contexte correct et faire la queue d’un rendu.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Utiliser \@la clé pour contrôler la préservation des éléments et des composants

Lors du rendu d’une liste d’éléments ou de Blazorcomposants et les éléments ou composants changent par la suite, l’algorithme de diffing doit décider lequel des éléments ou composants précédents peut être conservé et comment les objets modèles doivent les cartographier. Normalement, ce processus est automatique et peut être ignoré, mais il ya des cas où vous pouvez contrôler le processus.

Prenons l’exemple suivant :

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

Le contenu `People` de la collection peut changer avec des entrées insérées, supprimées ou réédiffues. Lorsque le composant rerenders, le `<DetailsEditor>` composant `Details` peut changer pour recevoir différentes valeurs de paramètres. Cela peut entraîner un rerendering plus complexe que prévu. Dans certains cas, le rerendering peut conduire à des différences de comportement visibles, telles que la concentration d’éléments perdus.

Le processus de cartographie peut [`@key`](xref:mvc/views/razor#key) être contrôlé avec l’attribut de directive. `@key`oblige l’algorithme à garantir la préservation d’éléments ou de composants en fonction de la valeur de la clé :

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

Lorsque `People` la collection change, l’algorithme de `<DetailsEditor>` diffing conserve l’association entre les instances et `person` les instances :

* Si `Person` un a est `People` supprimé de `<DetailsEditor>` la liste, seule l’instance correspondante est supprimée de l’interface utilisateur. D’autres cas sont laissés inchangés.
* Si `Person` un est inséré à une position `<DetailsEditor>` dans la liste, un nouvel exemple est inséré à cette position correspondante. D’autres cas sont laissés inchangés.
* Si `Person` les entrées sont réin commandées, les instances correspondantes `<DetailsEditor>` sont conservées et réinservées dans l’interface utilisateur.

Dans certains scénarios, `@key` l’utilisation de minimise la complexité du rerendering et évite les problèmes potentiels avec les parties étatiques du DOM changeant, telles que la position de mise au point.

> [!IMPORTANT]
> Les clés sont locales à chaque élément ou composant de conteneur. Les clés ne sont pas comparées globalement dans l’ensemble du document.

### <a name="when-to-use-key"></a>Quand utiliser \@la clé

Typiquement, il est `@key` logique d’utiliser chaque fois qu’une liste est rendue (par exemple, dans un `@foreach` bloc) et une valeur appropriée existe pour définir le `@key`.

Vous pouvez `@key` également Blazor utiliser pour empêcher de préserver un élément ou un sous-arbre composant lorsqu’un objet change :

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

En `@currentPerson` cas `@key` de modification, la directive d’attribut oblige Blazor à jeter l’ensemble `<div>` et ses descendants et à reconstruire le sous-arbre au sein de l’interface utilisateur avec de nouveaux éléments et composants. Cela peut être utile si vous avez besoin de `@currentPerson` garantir qu’aucun état d’interface utilisateur n’est préservé lorsque les changements.

### <a name="when-not-to-use-key"></a>Quand ne \@pas utiliser la clé

Il ya un coût de performance `@key`lors de diffing avec . Le coût de performance n’est `@key` pas élevé, mais ne précise que si le contrôle de l’élément ou des règles de préservation des composants bénéficient à l’application.

Même `@key` s’il n’est pas utilisé, Blazor préserve autant que possible les instances d’éléments et d’éléments pour enfants. Le seul avantage `@key` à utiliser est le contrôle sur la *façon dont* les instances du modèle sont cartographiés aux instances des composants préservés, au lieu de l’algorithme de diffing sélectionnant la cartographie.

### <a name="what-values-to-use-for-key"></a>Quelles valeurs utiliser \@pour la clé

En général, il est logique de fournir `@key`l’un des types suivants de valeur pour :

* Exemple d’objets modèles `Person` (par exemple, un cas comme dans l’exemple précédent). Cela garantit la préservation basée sur l’égalité de référence des objets.
* Identifiants uniques (par exemple, valeurs `int` `string`clés `Guid`primaires de type, , ou ).

Assurez-vous que `@key` les valeurs utilisées pour ne pas s’opposer. Si des valeurs contradictoires sont détectées Blazor dans le même élément parent, jette une exception parce qu’elle ne peut pas cartographier de façon déterministe de vieux éléments ou composants à de nouveaux éléments ou composants. N’utilisez que des valeurs distinctes, telles que des instances d’objets ou des valeurs clés primaires.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Ne créez pas de composants qui écrivent à leurs propres propriétés de paramètres

Les paramètres sont dépassés dans les conditions suivantes :

* Le contenu d’un composant enfant `RenderFragment`est rendu avec un .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé dans la composante parente.

Les paramètres sont réinitialisés <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> parce que le composant parent rerenders quand est appelé et de nouvelles valeurs de paramètres sont fournis à la composante enfant.

Considérez `Expander` le composant suivant qui :

* Rend le contenu de l’enfant.
* Bascules montrant le contenu de l’enfant avec un paramètre de composant.

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

Le `Expander` composant est ajouté à un `StateHasChanged`composant parent qui peut appeler :

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Initialement, `Expander` les composants se comportent `Expanded` indépendamment lorsque leurs propriétés sont basculer. Les composants de l’enfant maintiennent leurs états comme prévu. Lorsqu’il `StateHasChanged` est appelé `Expanded` chez le parent, le paramètre du`true`premier composant enfant est réinitialisé à sa valeur initiale ( ). La `Expander` valeur du `Expanded` deuxième composant n’est pas réinitialisée parce qu’aucun contenu pour enfants n’est rendu dans le deuxième composant.

Pour maintenir l’état dans le scénario `Expander` précédent, utilisez un champ *privé* dans la composante pour maintenir son état basculeux.

Le `Expander` composant suivant :

* Accepte la `Expanded` valeur du paramètre de composant du parent.
* Attribue la valeur du paramètre`_expanded`de composant à un champ *privé* ( ) dans [l’événement OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Utilise le champ privé pour maintenir son état de basculement interne.

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

## <a name="partial-class-support"></a>Soutien partiel de la classe

Les composants de rasoir sont générés en tant que classes partielles. Les composants razor sont rédigés à l’aide de l’une ou l’autre des approches suivantes :

* Le code C est [`@code`](xref:mvc/views/razor#code) défini dans un bloc avec le balisage HTML et le code Razor dans un seul fichier. Blazordéfinissent leurs composants Razor à l’aide de cette approche.
* Le code C est placé dans un fichier de code-derrière défini comme une classe partielle.

L’exemple suivant `Counter` montre le `@code` composant par défaut Blazor avec un bloc dans une application générée à partir d’un modèle. Le balisage HTML, le code Razor et le code C sont dans le même fichier :

*Counter.razor*:

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

Le `Counter` composant peut également être créé à l’aide d’un fichier de code-derrière avec une classe partielle:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

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

Ajoutez tous les espaces de nom requis au fichier de classe partielle au besoin. Les espaces nominaux typiques utilisés par les composants Razor comprennent :

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Spécifier une classe de base

La [`@inherits`](xref:mvc/views/razor#inherits) directive peut être utilisée pour spécifier une classe de base pour un composant. L’exemple suivant montre comment un composant `BlazorRocksBase`peut hériter d’une classe de base, pour fournir les propriétés et les méthodes du composant. La classe de `ComponentBase`base devrait dériver de .

*Pages/BlazorRocks.razor*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

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

## <a name="specify-an-attribute"></a>Spécifier un attribut

Les attributs peuvent être spécifiés [`@attribute`](xref:mvc/views/razor#attribute) dans les composants Razor avec la directive. L’exemple suivant `[Authorize]` applique l’attribut à la classe des composants :

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Composants d’importation

Le namespace d’un composant rédigé avec Razor est basé sur (dans l’ordre prioritaire):

* [`@namespace`](xref:mvc/views/razor#namespace)désignation dans le fichier Razor (`@namespace BlazorSample.MyNamespace`*.razor*) balisage ( ).
* Le projet `RootNamespace` est dans le`<RootNamespace>BlazorSample</RootNamespace>`dossier du projet ( ).
* Le nom du projet, tiré du nom de fichier du fichier du projet *(.csproj*), et le chemin de la racine du projet à la composante. Par exemple, le cadre résout *à* l’espace*BlazorSample.csproj* `BlazorSample.Pages`nom . Les composants suivent les règles contraignantes du nom C. Pour `Index` le composant de cet exemple, les composantes de portée sont tous les composants :
  * Dans le même dossier, *Pages*.
  * Les composants de la racine du projet qui ne spécifient pas explicitement un espace de nom différent.

Les composants définis dans un espace de nom [`@using`](xref:mvc/views/razor#using) différent sont mis en portée à l’aide de la directive de Razor.

Si un `NavMenu.razor`autre composant, , existe dans le *BlazorSample/Shared/* `Index.razor` dossier, `@using` le composant peut être utilisé avec l’instruction suivante:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Les composants peuvent également être référencés à l’aide de leurs noms entièrement qualifiés, ce qui n’exige pas la [`@using`](xref:mvc/views/razor#using) directive :

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> La `global::` qualification n’est pas soutenue.
>
> L’importation de composants `using` avec des `@using Foo = Bar`relevés alias (par exemple, ) n’est pas prise en charge.
>
> Les noms partiellement qualifiés ne sont pas pris en charge. Par exemple, `@using BlazorSample` l’ajout et le référencement `NavMenu.razor` avec `<Shared.NavMenu></Shared.NavMenu>` n’est pas pris en charge.

## <a name="conditional-html-element-attributes"></a>Attributs conditionnels d’élément HTML

Les attributs d’élément HTML sont rendus sous condition en fonction de la valeur .NET. Si la `false` valeur `null`est ou, l’attribut n’est pas rendu. Si la `true`valeur est, l’attribut est rendu minimisé.

Dans l’exemple `IsCompleted` suivant, `checked` détermine si elle est rendue dans la majoration de l’élément :

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Si `IsCompleted` `true`c’est le cas, la case à cocher est rendue comme:

```html
<input type="checkbox" checked />
```

Si `IsCompleted` `false`c’est le cas, la case à cocher est rendue comme:

```html
<input type="checkbox" />
```

Pour plus d’informations, consultez <xref:mvc/views/razor>.

> [!WARNING]
> Certains attributs HTML, tels que [aria-pressé,](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)ne fonctionnent pas correctement `bool`lorsque le type .NET est un . Dans ces cas, `string` utilisez un `bool`type au lieu d’un .

## <a name="raw-html"></a>HTML brut

Les cordes sont normalement rendues à l’aide de nœuds texte DOM, ce qui signifie que toute balisage qu’ils peuvent contenir est ignorée et traitée comme un texte littéral. Pour rendre HTML brut, enveloppez `MarkupString` le contenu HTML dans une valeur. La valeur est analysée sous forme de HTML ou de SVG et insérée dans le DOM.

> [!WARNING]
> Rendre HTML brut construit à partir de n’importe quelle source non fiable est un **risque pour la sécurité** et doit être évité!

L’exemple suivant `MarkupString` montre l’utilisation du type pour ajouter un bloc de contenu HTML statique à la sortie rendue d’un composant :

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Valeurs et paramètres en cascade

Dans certains scénarios, il est gênant de faire circuler des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composants,](#component-parameters)surtout lorsqu’il y a plusieurs couches de composants. Les valeurs et les paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant d’ancêtre de fournir une valeur à tous ses composants descendants. Les valeurs et les paramètres en cascade offrent également une approche pour les composants à coordonner.

### <a name="theme-example"></a>Exemple de thème

Dans l’exemple suivant de `ThemeInfo` l’application d’échantillon, la classe spécifie les informations thématiques pour circuler dans la hiérarchie des composants afin que tous les boutons d’une partie donnée de l’application partagent le même style.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Un composant d’ancêtre peut fournir une valeur en cascade à l’aide du composant valeur en cascade. Le `CascadingValue` composant enveloppe un sous-ensemble de la hiérarchie des composants et fournit une valeur unique à tous les composants de ce sous-arbre.

Par exemple, l’application de l’échantillon spécifie les informations thématiques (`ThemeInfo`) dans l’une des `@Body` mises en page de l’application comme paramètre en cascade pour tous les composants qui composent le corps de mise en page de la propriété. `ButtonClass`se voit attribuer `btn-success` une valeur de la composante de mise en page. N’importe quel composant descendant `ThemeInfo` peut consommer cette propriété par l’objet en cascade.

`CascadingValuesParametersLayout`Composant:

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

Pour utiliser les valeurs en cascade, les composants déclarent `[CascadingParameter]` les paramètres en cascade à l’aide de l’attribut. Les valeurs en cascade sont liées aux paramètres en cascade par type.

Dans l’application `CascadingValuesParametersTheme` d’échantillon, `ThemeInfo` le composant lie la valeur en cascade à un paramètre en cascade. Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.

`CascadingValuesParametersTheme`Composant:

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

Pour en cascader plusieurs valeurs du même type `Name` dans le `CascadingValue` même sous-arbre, fournir une chaîne unique à chaque composant et son correspondant `CascadingParameter`. Dans l’exemple `CascadingValue` suivant, deux composants `MyCascadingType` cascadent différents exemples de par nom:

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

Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans la composante ancêtre par leur nom :

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Exemple de TabSet

Les paramètres en cascade permettent également aux composants de collaborer à travers la hiérarchie des composants. Par exemple, considérez l’exemple *TabSet* suivant dans l’application d’échantillon.

L’application d’échantillon a une `ITab` interface qui implémente des onglets :

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Le `CascadingValuesParametersTabSet` composant `TabSet` utilise le composant, qui contient plusieurs `Tab` composants :

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

Les `Tab` composants de l’enfant ne sont `TabSet`pas explicitement adoptés comme paramètres de la . Au lieu `Tab` de cela, les composants de `TabSet`l’enfant font partie du contenu de l’enfant de la . Cependant, `TabSet` le besoin doit `Tab` encore connaître chaque composant afin qu’il puisse rendre les en-têtes et l’onglet actif. Pour permettre cette coordination sans `TabSet` nécessiter de code supplémentaire, le composant *peut se fournir comme une valeur en cascade* qui est ensuite captée par les composants descendants. `Tab`

`TabSet`Composant:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Les `Tab` composants descendants `TabSet` capturent le contenant comme `Tab` paramètre en cascade, de sorte que les composants s’ajoutent à l’onglet `TabSet` et coordonnent sur quel onglet est actif.

`Tab`Composant:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Modèles de rasoir

Les fragments de rendu peuvent être définis à l’aide de la syntaxe du modèle Razor. Les modèles de rasoir sont un moyen de définir un extrait d’interface utilisateur et d’assumer le format suivant :

```razor
@<{HTML tag}>...</{HTML tag}>
```

L’exemple suivant illustre `RenderFragment` comment `RenderFragment<T>` spécifier et valeurs et rendre les modèles directement dans un composant. Les fragments de rendu peuvent également être adoptés comme arguments aux [composants modélistes.](xref:blazor/templated-components)

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

Sortie rendue du code précédent :

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Images évolutives de Vector Graphics (SVG)

Depuis Blazor rend HTML, les images prises en charge par navigateur, y compris les images Scalable Vector Graphics (SVG) (*.svg*), sont prises en charge via le `<img>` tag :

```html
<img alt="Example image" src="some-image.svg" />
```

De même, les images SVG sont prises en charge dans les règles CSS d’un fichier de feuille de style (*.css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Cependant, la balisage SVG en ligne n’est pas pris en charge dans tous les scénarios. Si vous `<svg>` placez une balise directement dans un fichier composant (*.razor*), le rendu d’image de base est pris en charge, mais de nombreux scénarios avancés ne sont pas encore pris en charge. Par exemple, `<use>` les balises `@bind` ne sont pas actuellement respectées et ne peuvent pas être utilisées avec certaines balises SVG. Nous prévoyons répondre à ces limites dans une version future.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/server>&ndash; Inclut des Blazor conseils sur la création d’applications Server qui doivent faire face à l’épuisement des ressources.
