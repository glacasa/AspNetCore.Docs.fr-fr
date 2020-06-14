---
title: Créer et utiliser des Razor composants ASP.net Core
author: guardrex
description: Découvrez comment créer et utiliser des Razor composants, notamment comment lier des données, gérer des événements et gérer des cycles de vie de composant.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: 2a6de1a39737f98cb151a0556f36c223d86f9752
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84723949"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Créer et utiliser des Razor composants ASP.net Core

Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Tobias Bartsch](https://www.aveo-solutions.com/)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

Blazorles applications sont générées à l’aide de *composants*. Un composant est un bloc d’interface utilisateur (IU) autonome, tel qu’une page, une boîte de dialogue ou un formulaire. Un composant comprend le balisage HTML et la logique de traitement requise pour injecter des données ou répondre à des événements d’interface utilisateur. Les composants sont flexibles et légers. Elles peuvent être imbriquées, réutilisées et partagées entre les projets.

## <a name="component-classes"></a>Classes de composant

Les composants sont implémentés dans les [Razor](xref:mvc/views/razor) fichiers de composants (*. Razor*) à l’aide d’une combinaison de balisage C# et html. Un composant dans Blazor est officiellement désigné sous le terme de * Razor composant*.

Le nom d’un composant doit commencer par un caractère majuscule. Par exemple, *MyCoolComponent. Razor* est valide et *MyCoolComponent. Razor* n’est pas valide.

L’interface utilisateur d’un composant est définie à l’aide de HTML. La logique de rendu dynamique (par exemple, les boucles, les instructions conditionnelles, les expressions) est ajoutée à l’aide d’une syntaxe C# incorporée appelée *Razor* . Quand une application est compilée, le balisage HTML et la logique de rendu C# sont convertis en une classe de composant. Le nom de la classe générée correspond au nom du fichier.

Les membres de la classe Component sont définis dans un [`@code`][1] bloc. Dans le [`@code`][1] bloc, l’état du composant (propriétés, champs) est spécifié avec des méthodes pour la gestion des événements ou pour la définition d’une autre logique de composant. Plus d’un [`@code`][1] bloc est autorisé.

Les membres de composant peuvent être utilisés dans le cadre de la logique de rendu du composant à l’aide d’expressions C# qui commencent par `@` . Par exemple, un champ C# est rendu en préfixant `@` le nom du champ. L’exemple suivant évalue et affiche :

* `headingFontStyle`à la valeur de propriété CSS pour `font-style` .
* `headingText`au contenu de l' `<h1>` élément.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Une fois le composant restitué initialement, le composant régénère son arborescence de rendu en réponse aux événements. Blazorcompare ensuite la nouvelle arborescence de rendu à la précédente et applique toutes les modifications apportées au Document Object Model du navigateur (DOM).

Les composants sont des classes C# ordinaires qui peuvent être placées n’importe où dans un projet. Les composants qui produisent des pages Web résident généralement dans le dossier *pages* . Les composants qui ne sont pas des pages sont souvent placés dans le dossier *partagé* ou dans un dossier personnalisé ajouté au projet.

En règle générale, l’espace de noms d’un composant est dérivé de l’espace de noms racine de l’application et de l’emplacement (dossier) du composant dans l’application. Si l’espace de noms racine de l’application est `BlazorApp` et que le `Counter` composant se trouve dans le dossier *pages* :

* L' `Counter` espace de noms du composant est `BlazorApp.Pages` .
* Le nom de type qualifié complet du composant est `BlazorApp.Pages.Counter` .

Pour les dossiers personnalisés qui contiennent des composants, ajoutez une `using` instruction au composant parent ou au fichier *_Imports. Razor* de l’application. L’exemple suivant rend les composants du dossier *composants* disponibles :

```razor
@using BlazorApp.Components
```

Un composant peut également être référencé directement :

```razor
<BlazorApp.Components.MyCoolComponent />
```

Pour plus d’informations, consultez la section [importer des composants](#import-components) .

## <a name="razor-syntax"></a>Syntaxe de Razor

Razorles composants dans les Blazor applications utilisent largement la Razor syntaxe. Si vous n’êtes pas familiarisé avec le Razor langage de balisage, nous vous recommandons de lire <xref:mvc/views/razor> avant de continuer.

Lorsque vous accédez au contenu sur la Razor syntaxe, portez une attention particulière aux sections suivantes :

* [Directives](xref:mvc/views/razor#directives): `@` -préfixe des mots clés réservés qui modifient généralement la manière dont le balisage du composant est analysé ou fonction.
* [Attributs de directive](xref:mvc/views/razor#directive-attributes): `@` Mots clés réservés préfixés qui modifient généralement le mode d’analyse ou de fonction des éléments de composant.

## <a name="static-assets"></a>Les ressources statiques

Blazorsuit la Convention de ASP.NET Core les applications qui placent des ressources statiques dans le [dossier racine Web (Wwwroot)](xref:fundamentals/index#web-root)du projet.

Utilisez un chemin d’accès relatif à `/` la base () pour faire référence à la racine Web d’une ressource statique. Dans l’exemple suivant, *logo.png* se trouve physiquement dans le dossier *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razorles composants ne prennent **pas** en charge la notation tilde-slash ( `~/` ).

Pour plus d’informations sur la définition du chemin d’accès de base d’une application, consultez <xref:host-and-deploy/blazor/index#app-base-path> .

## <a name="tag-helpers-arent-supported-in-components"></a>Les balises tag Helper ne sont pas prises en charge dans les composants

Les [tag helpers](xref:mvc/views/tag-helpers/intro) ne sont pas pris en charge dans les Razor composants (fichiers *. Razor* ). Pour fournir des fonctionnalités semblables à tag Helper dans Blazor , créez un composant avec les mêmes fonctionnalités que le tag Helper et utilisez le composant à la place.

## <a name="use-components"></a>Utiliser des composants

Les composants peuvent inclure d’autres composants en les déclarant à l’aide de la syntaxe d’élément HTML. Le balisage pour l’utilisation d’un composant ressemble à une balise HTML où le nom de la balise est le type du composant.

Le balisage suivant dans *index. Razor* rend une `HeadingComponent` instance de :

```razor
<HeadingComponent />
```

*Composants/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Si un composant contient un élément HTML avec une première lettre majuscule qui ne correspond pas à un nom de composant, un avertissement est émis pour indiquer que l’élément a un nom inattendu. L’ajout d’une [`@using`][2] directive pour l’espace de noms du composant rend le composant disponible, ce qui résout l’avertissement.

## <a name="routing"></a>Routage

Le routage dans Blazor est effectué en fournissant un modèle de routage à chaque composant accessible dans l’application.

Lorsqu’un Razor fichier avec une [`@page`][9] directive est compilé, la classe générée reçoit un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> qui spécifie le modèle de routage. Lors de l’exécution, le routeur recherche les classes de composant avec un <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> et rend le composant qui a un modèle de routage correspondant à l’URL demandée.

```razor
@page "/ParentComponent"

...
```

Pour plus d’informations, consultez <xref:blazor/routing>.

## <a name="parameters"></a>Paramètres

### <a name="route-parameters"></a>Paramètres d’itinéraire

Les composants peuvent recevoir des paramètres de routage du modèle de routage fourni dans la [`@page`][9] directive. Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants.

*Pages/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Les paramètres facultatifs ne sont pas pris en charge [`@page`][9] . deux directives sont donc appliquées dans l’exemple précédent. La première permet de naviguer jusqu’au composant sans paramètre. La deuxième [`@page`][9] directive reçoit le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.

La syntaxe de paramètre *catch-all* ( `*` / `**` ), qui capture le chemin d’accès dans plusieurs limites de dossiers, n’est **pas** prise en charge dans les Razor composants (*. Razor*).

### <a name="component-parameters"></a>Paramètres de composant

Les composants peuvent avoir des *paramètres de composant*, qui sont définis à l’aide de propriétés publiques sur la classe de composant avec l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut. Utilisez des attributs pour spécifier des arguments pour un composant dans le balisage.

*Composants/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Dans l’exemple suivant tiré de l’exemple d’application, le `ParentComponent` définit la valeur de la `Title` propriété de `ChildComponent` .

*Pages/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Ne créez pas de composants qui écrivent dans leurs propres *paramètres de composant*, utilisez un champ privé à la place. Pour plus d’informations, consultez la section [ne pas créer de composants qui écrivent dans leur propre propriété de paramètre](#dont-create-components-that-write-to-their-own-parameter-properties) .

## <a name="child-content"></a>Contenu enfant

Les composants peuvent définir le contenu d’un autre composant. Le composant d’affectation fournit le contenu entre les balises qui spécifient le composant récepteur.

Dans l’exemple suivant, `ChildComponent` a une `ChildContent` propriété qui représente un <xref:Microsoft.AspNetCore.Components.RenderFragment> , qui représente un segment de l’interface utilisateur à restituer. La valeur de `ChildContent` est positionnée dans le balisage du composant où le contenu doit être rendu. La valeur de `ChildContent` est reçue du composant parent et rendue à l’intérieur du panneau de démarrage `panel-body` .

*Composants/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> La propriété qui reçoit le <xref:Microsoft.AspNetCore.Components.RenderFragment> contenu doit être nommée `ChildContent` par Convention.

`ParentComponent`Dans l’exemple d’application, vous pouvez fournir du contenu pour le rendu de `ChildComponent` en plaçant le contenu à l’intérieur des `<ChildComponent>` balises.

*Pages/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Réprojection d’attribut et paramètres arbitraires

Les composants peuvent capturer et restituer des attributs supplémentaires en plus des paramètres déclarés du composant. Des attributs supplémentaires peuvent être capturés dans un dictionnaire *, puis* réintégrés à un élément lorsque le composant est rendu à l’aide de la [`@attributes`][3] Razor directive. Ce scénario est utile lors de la définition d’un composant qui produit un élément de balisage qui prend en charge diverses personnalisations. Par exemple, il peut être fastidieux de définir des attributs séparément pour un `<input>` qui prend en charge de nombreux paramètres.

Dans l’exemple suivant, le premier `<input>` élément ( `id="useIndividualParams"` ) utilise des paramètres de composant individuels, tandis que le deuxième `<input>` élément ( `id="useAttributesDict"` ) utilise la projection d’attributs :

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

Le type du paramètre doit implémenter `IEnumerable<KeyValuePair<string, object>>` avec des clés de chaîne. L’utilisation `IReadOnlyDictionary<string, object>` de est également une option dans ce scénario.

Les éléments rendus `<input>` à l’aide des deux approches sont identiques :

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

Pour accepter des attributs arbitraires, définissez un paramètre de composant à l’aide de l' [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut avec la <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriété définie sur `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

La <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> propriété sur [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) permet au paramètre de correspondre à tous les attributs qui ne correspondent à aucun autre paramètre. Un composant ne peut définir qu’un seul paramètre avec <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> . Le type de propriété utilisé avec <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> doit pouvoir être assigné à partir de `Dictionary<string, object>` avec des clés de chaîne. `IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` sont également des options dans ce scénario.

La position [`@attributes`][3] relative à la position des attributs d’élément est importante. Quand sont représentées [`@attributes`][3] sur l’élément, les attributs sont traités de droite à gauche (dernier à premier). Prenons l’exemple suivant d’un composant qui consomme un `Child` composant :

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

L' `Child` attribut du composant `extra` est défini à droite de [`@attributes`][3] . Le `Parent` rendu du composant `<div>` contient `extra="5"` lorsqu’il est transmis via l’attribut supplémentaire, car les attributs sont traités de droite à gauche (dernier à premier) :

```html
<div extra="5" />
```

Dans l’exemple suivant, l’ordre de `extra` et [`@attributes`][3] est inversé dans le d' `Child` un composant `<div>` :

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Le rendu `<div>` dans le `Parent` composant contient `extra="10"` lorsqu’il est passé par le biais de l’attribut supplémentaire :

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Capturer des références à des composants

Les références de composant offrent un moyen de référencer une instance de composant afin que vous puissiez émettre des commandes vers cette instance, telles que `Show` ou `Reset` . Pour capturer une référence de composant :

* Ajoutez un [`@ref`][4] attribut au composant enfant.
* Définissez un champ avec le même type que le composant enfant.

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

Lors du rendu du composant, le `loginDialog` champ est rempli avec l' `MyLoginDialog` instance du composant enfant. Vous pouvez ensuite appeler des méthodes .NET sur l’instance du composant.

> [!IMPORTANT]
> La `loginDialog` variable est remplie uniquement après le rendu du composant et sa sortie comprend l' `MyLoginDialog` élément. Jusqu’à ce stade, il n’y a rien à référencer. Pour manipuler des références de composants après la fin du rendu du composant, utilisez les [méthodes OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).

Pour référencer des composants dans une boucle, consultez [capturer des références à plusieurs composants enfants similaires (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Bien que la capture de références de composant utilise une syntaxe similaire pour [capturer des références d’élément](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), il ne s’agit pas d’une fonctionnalité JavaScript Interop. Les références de composant ne sont pas passées au code JavaScript. Les références de composants sont uniquement utilisées dans le code .NET.

> [!NOTE]
> N’utilisez **pas** de références de composant pour muter l’état des composants enfants. Utilisez plutôt des paramètres déclaratifs normaux pour passer des données aux composants enfants. L’utilisation de paramètres déclaratifs normaux entraîne le rerendu automatique des composants enfants.

## <a name="invoke-component-methods-externally-to-update-state"></a>Appeler des méthodes de composant en externe pour mettre à jour l’État

Blazorutilise un contexte de synchronisation ( <xref:System.Threading.SynchronizationContext> ) pour appliquer un seul thread logique d’exécution. Les méthodes de [cycle de vie](xref:blazor/lifecycle) d’un composant et les rappels d’événements déclenchés par Blazor sont exécutés sur le contexte de synchronisation.

BlazorLe contexte de synchronisation du serveur tente d’émuler un environnement monothread afin qu’il corresponde étroitement au modèle webassembly dans le navigateur, qui est mono-thread. À un moment donné, le travail est effectué sur un seul thread, ce qui donne l’impression d’un seul thread logique. Deux opérations ne sont pas exécutées simultanément.

Dans le cas où un composant doit être mis à jour en fonction d’un événement externe, tel qu’un minuteur ou d’autres notifications, utilisez la `InvokeAsync` méthode, qui est distribuée au Blazor contexte de synchronisation de. Par exemple, considérez un *service de notification* qui peut notifier n’importe quel composant d’écoute de l’État mis à jour :

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

Inscrivez le `NotifierService` en tant que singletion :

* Dans Blazor Webassembly, inscrivez le service dans `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Dans Blazor serveur, inscrivez le service dans `Startup.ConfigureServices` :

  ```csharp
  services.AddScoped<NotifierService>();
  ```

Utilisez `NotifierService` pour mettre à jour un composant :

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

Dans l’exemple précédent, `NotifierService` appelle la méthode du composant `OnNotify` en dehors du Blazor contexte de synchronisation de. `InvokeAsync`est utilisé pour basculer vers le contexte correct et pour la mise en file d’attente d’un rendu.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Utiliser \@ la clé pour contrôler la conservation des éléments et des composants

Lors du rendu d’une liste d’éléments ou de composants, et si les éléments ou les composants changent par la suite, l' Blazor algorithme de différenciation de doit décider quels éléments ou composants précédents peuvent être conservés et comment les objets de modèle doivent être mappés à eux. Normalement, ce processus est automatique et peut être ignoré, mais dans certains cas, il peut être utile de contrôler le processus.

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

Le contenu de la `People` collection peut changer avec des entrées insérées, supprimées ou réorganisées. Lors du rerendu du composant, le `<DetailsEditor>` composant peut changer pour recevoir des `Details` valeurs de paramètre différentes. Cela peut entraîner un rerendu plus complexe que prévu. Dans certains cas, le rerendu peut entraîner des différences de comportement visibles, telles que le focus de l’élément perdu.

Le processus de mappage peut être contrôlé à l’aide de l' [`@key`][5] attribut directive. [`@key`][5]force l’algorithme de comparaison à garantir la préservation des éléments ou des composants en fonction de la valeur de la clé :

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

Lorsque la `People` collection est modifiée, l’algorithme de comparaison conserve l’association entre les `<DetailsEditor>` instances et les `person` instances :

* Si un `Person` est supprimé de la `People` liste, seule l' `<DetailsEditor>` instance correspondante est supprimée de l’interface utilisateur. Les autres instances restent inchangées.
* Si un `Person` est inséré à une position dans la liste, une nouvelle `<DetailsEditor>` instance est insérée à cette position correspondante. Les autres instances restent inchangées.
* Si `Person` les entrées sont réordonnées, les `<DetailsEditor>` instances correspondantes sont conservées et réordonnées dans l’interface utilisateur.

Dans certains scénarios, l’utilisation de [`@key`][5] réduit la complexité du rerendu et évite les problèmes potentiels liés aux parties avec état de la modification du modèle DOM, telles que la position du focus.

> [!IMPORTANT]
> Les clés sont locales pour chaque élément ou composant conteneur. Les clés ne sont pas comparées globalement dans le document.

### <a name="when-to-use-key"></a>Quand utiliser la \@ clé

En règle générale, il est judicieux d’utiliser [`@key`][5] chaque fois qu’une liste est affichée (par exemple, dans un bloc [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) et qu’une valeur appropriée existe pour définir [`@key`][5] .

Vous pouvez également utiliser [`@key`][5] pour empêcher Blazor de conserver un élément ou une sous-arborescence de composants lorsqu’un objet change :

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

En cas `@currentPerson` de modification, la [`@key`][5] directive Blazor d’attribut force à ignorer la totalité `<div>` et ses descendants, et à reconstruire la sous-arborescence de l’interface utilisateur avec de nouveaux éléments et composants. Cela peut être utile si vous devez garantir qu’aucun État d’interface utilisateur n’est préservé lorsque des `@currentPerson` modifications sont apportées.

### <a name="when-not-to-use-key"></a>Quand ne pas utiliser la \@ clé

Il y a un coût en matière de performances lors de la comparaison avec [`@key`][5] . Le coût des performances n’est pas important, mais spécifiez uniquement [`@key`][5] si les règles de conservation des éléments ou des composants bénéficient de l’application.

Même si [`@key`][5] n’est pas utilisé, Blazor conserve autant que possible les instances d’élément et de composant enfants. Le seul avantage de l’utilisation de [`@key`][5] est de contrôler la *façon dont* les instances de modèle sont mappées aux instances de composant conservées, au lieu de l’algorithme de comparaison qui sélectionne le mappage.

### <a name="what-values-to-use-for-key"></a>Valeurs à utiliser pour la \@ clé

En règle générale, il est logique de fournir l’un des types de valeur suivants pour [`@key`][5] :

* Instances d’objet de modèle (par exemple, une `Person` instance comme dans l’exemple précédent). Cela garantit la préservation en fonction de l’égalité des références d’objet.
* Identificateurs uniques (par exemple, les valeurs de clé primaire de type `int` , `string` ou `Guid` ).

Vérifiez que les valeurs utilisées pour [`@key`][5] ne sont pas en conflit. Si les valeurs en conflit sont détectées dans le même élément parent, Blazor lève une exception, car il ne peut pas mapper de manière déterministe les anciens éléments ou composants aux nouveaux éléments ou composants. Utilisez uniquement des valeurs distinctes, telles que des instances d’objets ou des valeurs de clé primaire.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Ne créez pas de composants qui écrivent dans leurs propres propriétés de paramètre

Les paramètres sont remplacés dans les conditions suivantes :

* Le contenu d’un composant enfant est rendu avec un <xref:Microsoft.AspNetCore.Components.RenderFragment> .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé dans le composant parent.

Les paramètres sont réinitialisés, car le composant parent <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est restitué à nouveau lorsque est appelé et de nouvelles valeurs de paramètres sont fournies au composant enfant.

Prenons le `Expander` composant suivant :

* Restitue le contenu enfant.
* Active ou désactive l’indication du contenu enfant avec un paramètre de composant.

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

Le `Expander` composant est ajouté à un composant parent qui peut appeler <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Au départ, les `Expander` composants se comportent indépendamment lorsque leurs `Expanded` propriétés sont basculées. Les composants enfants maintiennent leurs États comme prévu. Lorsque <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> est appelé dans le parent, le `Expanded` paramètre du premier composant enfant est réinitialisé à sa valeur initiale ( `true` ). La `Expander` valeur du deuxième composant `Expanded` n’est pas réinitialisée, car aucun contenu enfant n’est restitué dans le deuxième composant.

Pour maintenir l’État dans le scénario précédent, utilisez un *champ privé* dans le `Expander` composant pour maintenir son état bascule.

Le `Expander` composant suivant :

* Accepte la `Expanded` valeur de paramètre du composant à partir du parent.
* Affecte la valeur du paramètre de composant à un *champ privé* ( `expanded` ) dans l' [événement OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Utilise le champ privé pour conserver son état bascule interne.

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

## <a name="partial-class-support"></a>Prise en charge des classes partielles

Razorles composants sont générés en tant que classes partielles. Razorles composants sont créés à l’aide de l’une des approches suivantes :

* Le code C# est défini dans un [`@code`][1] bloc avec le balisage HTML et Razor le code dans un fichier unique. Blazorles modèles définissent leurs Razor composants à l’aide de cette approche.
* Le code C# est placé dans un fichier code-behind défini en tant que classe partielle.

L’exemple suivant montre le composant par défaut `Counter` avec un [`@code`][1] bloc dans une application générée à partir d’un Blazor modèle. Le balisage HTML, le Razor code et le code C# se trouvent dans le même fichier :

*Counter. Razor*:

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

Le `Counter` composant peut également être créé à l’aide d’un fichier code-behind avec une classe partielle :

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

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

Ajoutez tous les espaces de noms requis au fichier de classe partielle, si nécessaire. Les espaces de noms standard utilisés par les Razor composants sont les suivants :

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Spécifier une classe de base

La [`@inherits`][6] directive peut être utilisée pour spécifier une classe de base pour un composant. L’exemple suivant montre comment un composant peut hériter d’une classe de base, `BlazorRocksBase` , pour fournir les propriétés et les méthodes du composant. La classe de base doit dériver de <xref:Microsoft.AspNetCore.Components.ComponentBase> .

*Pages/BlazorRocks. Razor*:

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

Les attributs peuvent être spécifiés dans Razor les composants à l’aide de la [`@attribute`][7] directive. L’exemple suivant applique l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut à la classe de composant :

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importer des composants

L’espace de noms d’un composant créé avec Razor est basé sur (par ordre de priorité) :

* [`@namespace`][8]désignation dans le Razor balisage de fichier (*. Razor*) ( `@namespace BlazorSample.MyNamespace` ).
* Du projet `RootNamespace` dans le fichier projet ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* Nom du projet, pris à partir du nom de fichier du fichier projet (*. csproj*), et chemin d’accès de la racine du projet au composant. Par exemple, le Framework résout *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) en espace de noms `BlazorSample.Pages` . Les composants suivent les règles de liaison de noms C#. Pour le `Index` composant dans cet exemple, les composants de l’étendue sont tous les composants :
  * Dans le même dossier, *pages*.
  * Composants de la racine du projet qui ne spécifient pas explicitement un espace de noms différent.

Les composants définis dans un espace de noms différent sont placés dans la portée à l’aide Razor de [`@using`][2] la directive de.

Si un autre composant, `NavMenu.razor` , existe dans le dossier *BlazorSample/Shared/* , le composant peut être utilisé dans `Index.razor` avec l' [`@using`][2] instruction suivante :

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Les composants peuvent également être référencés à l’aide de leurs noms complets, ce qui ne requiert pas la [`@using`][2] directive :

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> La `global::` qualification n’est pas prise en charge.
>
> L’importation de composants avec des instructions [using](/dotnet/csharp/language-reference/keywords/using-statement) avec alias (par exemple, `@using Foo = Bar` ) n’est pas prise en charge.
>
> Les noms partiellement qualifiés ne sont pas pris en charge. Par exemple, `@using BlazorSample` l’ajout et la référencement du `NavMenu` composant ( `NavMenu.razor` ) avec `<Shared.NavMenu></Shared.NavMenu>` ne sont pas pris en charge.

## <a name="conditional-html-element-attributes"></a>Attributs d’éléments HTML conditionnels

Les attributs des éléments HTML sont rendus de manière conditionnelle en fonction de la valeur .NET. Si la valeur est `false` ou `null` , l’attribut n’est pas rendu. Si la valeur est `true` , l’attribut est rendu réduit.

Dans l’exemple suivant, `IsCompleted` détermine si `checked` est rendu dans le balisage de l’élément :

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Si `IsCompleted` est `true` , la case à cocher s’affiche comme suit :

```html
<input type="checkbox" checked />
```

Si `IsCompleted` est `false` , la case à cocher s’affiche comme suit :

```html
<input type="checkbox" />
```

Pour plus d’informations, consultez <xref:mvc/views/razor>.

> [!WARNING]
> Certains attributs HTML, tels que [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), ne fonctionnent pas correctement quand le type .net est `bool` . Dans ce cas, utilisez un `string` type au lieu d’un `bool` .

## <a name="raw-html"></a>HTML brut

Les chaînes sont normalement rendues à l’aide de nœuds de texte DOM, ce qui signifie que tout balisage qu’elles peuvent contenir est ignorée et traitée comme du texte littéral. Pour afficher le code HTML brut, encapsulez le contenu HTML dans une `MarkupString` valeur. La valeur est analysée au format HTML ou SVG et est insérée dans le DOM.

> [!WARNING]
> Le rendu de code HTML brut construit à partir de n’importe quelle source non approuvée constitue un risque pour la **sécurité** et doit être évité !

L’exemple suivant illustre l’utilisation du `MarkupString` type pour ajouter un bloc de contenu HTML statique à la sortie rendue d’un composant :

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Valeurs et paramètres en cascade

Dans certains scénarios, il est peu commode de transmettre des données d’un composant ancêtre à un composant descendant à l’aide de [paramètres de composant](#component-parameters), en particulier lorsqu’il existe plusieurs couches de composant. Les valeurs et paramètres en cascade résolvent ce problème en fournissant un moyen pratique pour un composant ancêtre de fournir une valeur à tous ses composants descendants. Les valeurs et les paramètres en cascade fournissent également une approche pour les composants à coordonner.

### <a name="theme-example"></a>Exemple de thème

Dans l’exemple suivant tiré de l’exemple d’application, la `ThemeInfo` classe spécifie les informations de thème pour descendre dans la hiérarchie des composants, de sorte que tous les boutons d’une partie donnée de l’application partagent le même style.

*UIThemeClasses/themeinfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Un composant ancêtre peut fournir une valeur en cascade à l’aide du composant de valeur en cascade. Le <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant encapsule une sous-arborescence de la hiérarchie des composants et fournit une valeur unique à tous les composants de cette sous-arborescence.

Par exemple, l’exemple d’application spécifie les informations de thème ( `ThemeInfo` ) dans l’une des dispositions de l’application en tant que paramètre en cascade pour tous les composants qui composent le corps de la disposition de la `@Body` propriété. `ButtonClass`la valeur est affectée `btn-success` à dans le composant Layout. Tout composant descendant peut consommer cette propriété par le biais de l' `ThemeInfo` objet en cascade.

`CascadingValuesParametersLayout`-

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

Pour utiliser des valeurs en cascade, les composants déclarent des paramètres en cascade à l’aide de l' [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut. Les valeurs en cascade sont liées aux paramètres en cascade par type.

Dans l’exemple d’application, le `CascadingValuesParametersTheme` composant lie la `ThemeInfo` valeur en cascade à un paramètre en cascade. Le paramètre est utilisé pour définir la classe CSS pour l’un des boutons affichés par le composant.

`CascadingValuesParametersTheme`-

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

Pour mettre en cascade plusieurs valeurs du même type dans la même sous-arborescence, fournissez une <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> chaîne unique à chaque <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composant et à son [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribut correspondant. Dans l’exemple suivant, deux <xref:Microsoft.AspNetCore.Components.CascadingValue%601> composants montent en cascade différentes instances de `MyCascadingType` par nom :

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

Dans un composant descendant, les paramètres en cascade reçoivent leurs valeurs des valeurs en cascade correspondantes dans le composant ancêtre par nom :

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Exemple TabSet

Les paramètres en cascade permettent également aux composants de collaborer au sein de la hiérarchie des composants. Par exemple, considérez l’exemple *TabSet* suivant dans l’exemple d’application.

L’exemple d’application possède une `ITab` interface qui implémente les onglets suivants :

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Le `CascadingValuesParametersTabSet` composant utilise le `TabSet` composant, qui contient plusieurs `Tab` composants :

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

Les `Tab` composants enfants ne sont pas explicitement passés comme paramètres à `TabSet` . Au lieu de cela, les `Tab` composants enfants font partie du contenu enfant de `TabSet` . Toutefois, le `TabSet` doit toujours connaître chaque `Tab` composant pour pouvoir afficher les en-têtes et l’onglet actif. Pour activer cette coordination sans nécessiter de code supplémentaire, le `TabSet` composant *peut se présenter comme une valeur en cascade* qui est ensuite récupérée par les `Tab` composants descendants.

`TabSet`-

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Les composants descendants `Tab` capturent le contenant `TabSet` comme paramètre en cascade, de sorte que les `Tab` composants s’ajoutent eux-mêmes à la et à la `TabSet` coordonnée sur laquelle l’onglet est actif.

`Tab`-

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razorceux

Les fragments de rendu peuvent être définis à l’aide de la Razor syntaxe de modèle. Razorles modèles sont un moyen de définir un extrait de code d’interface utilisateur et de supposer le format suivant :

```razor
@<{HTML tag}>...</{HTML tag}>
```

L’exemple suivant montre comment spécifier <xref:Microsoft.AspNetCore.Components.RenderFragment> des valeurs et <xref:Microsoft.AspNetCore.Components.RenderFragment%601> et restituer des modèles directement dans un composant. Les fragments de rendu peuvent également être passés comme arguments à des [composants basés](xref:blazor/templated-components)sur un modèle.

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

Sortie rendue du code précédent :

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Images SVG (Scalable Vector Graphics)

Étant donné que Blazor le rendu des images HTML, prises en charge par le navigateur, y compris les images SVG (Scalable Vector Graphics) (*. svg*), est pris en charge via la `<img>` balise :

```html
<img alt="Example image" src="some-image.svg" />
```

De même, les images SVG sont prises en charge dans les règles CSS d’un fichier de feuille de style (*. CSS*) :

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Toutefois, le balisage SVG en ligne n’est pas pris en charge dans tous les scénarios. Si vous placez une `<svg>` balise directement dans un fichier de composant (*. Razor*), le rendu d’image de base est pris en charge, mais de nombreux scénarios avancés ne sont pas encore pris en charge. Par exemple, les `<use>` balises ne sont pas actuellement respectées et [`@bind`][10] ne peuvent pas être utilisées avec certaines balises SVG. Pour plus d’informations, consultez [prise en charge SVG dans Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/server/threat-mitigation>: Fournit des conseils sur la création d' Blazor applications serveur qui doivent être en concurrence avec l’épuisement des ressources.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
