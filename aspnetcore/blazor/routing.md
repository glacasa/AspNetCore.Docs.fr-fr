---
title: itinéraire ASP.NET Blazor Core
author: guardrex
description: Découvrez comment acheminer les demandes dans les applications et sur le composant NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218893"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET itinéraire Core Blazor

Par [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Découvrez comment acheminer les `NavLink` demandes et comment utiliser le composant pour créer des liens de navigation dans les applications Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET’intégration de l’itinéraire de point de terminaison de base

Blazor Server est intégré dans [ASP.NET Core Endpoint Routing](xref:fundamentals/routing). Une application ASP.NET Core est configurée pour accepter les `MapBlazorHub` `Startup.Configure`connexions entrantes pour les composants interactifs avec :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

La configuration la plus typique est d’acheminer toutes les demandes vers une page Razor, qui agit comme l’hôte de la partie côté serveur de l’application Blazor Server. Par convention, la page *d’accueil* est généralement nommée *_Host.cshtml*. L’itinéraire spécifié dans le fichier hôte est appelé une *voie de repli* parce qu’il fonctionne avec une faible priorité dans l’appariement des itinéraires. La route de repli est envisagée lorsque d’autres itinéraires ne correspondent pas. Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l’application Blazor Server.

## <a name="route-templates"></a>Modèles d’itinéraires

Le `Router` composant permet le routage vers chaque composant avec un itinéraire spécifié. Le `Router` composant apparaît dans le fichier *App.razor* :

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Lorsqu’un fichier *.razor* avec une `@page` directive est <xref:Microsoft.AspNetCore.Components.RouteAttribute> compilé, la classe générée est fournie un spécifier le modèle d’itinéraire.

Au moment de `RouteView` l’exécution, le composant :

* Reçoit `RouteData` le `Router` de la avec tous les paramètres souhaités.
* Rend le composant spécifié avec sa mise en page (ou une mise en page par défaut facultative) à l’aide des paramètres spécifiés.

Vous pouvez spécifier en option un `DefaultLayout` paramètre avec une classe de mise en page à utiliser pour les composants qui ne spécifient pas une mise en page. Les modèles Blazor par `MainLayout` défaut spécifient le composant. *MainLayout.razor* se trouve dans le dossier *partagé* du projet de modèle. Pour plus d’informations <xref:blazor/layouts>sur les mises en page, voir .

Plusieurs modèles d’itinéraire peuvent être appliqués à un composant. Le composant suivant répond `/BlazorRoute` aux `/DifferentBlazorRoute`demandes et :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Pour que les URL se résolvent `<base>` correctement, l’application doit inclure une balise dans son fichier *wwwroot/index.html* (Blazor WebAssembly) ou *Pages/_Host.cshtml* fichier (Blazor Server) avec le chemin de base de l’application spécifié dans l’attribut `href` (`<base href="/">`). Pour plus d’informations, consultez <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fournir du contenu personnalisé lorsque le contenu n’est pas trouvé

Le `Router` composant permet à l’application de spécifier du contenu personnalisé si le contenu n’est pas trouvé pour l’itinéraire demandé.

Dans le fichier *App.razor,* définissez du contenu personnalisé dans le `NotFound` paramètre du modèle du `Router` composant :

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Le contenu `<NotFound>` des balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs. Pour appliquer une `NotFound` mise en <xref:blazor/layouts>page par défaut sur le contenu, voir .

## <a name="route-to-components-from-multiple-assemblies"></a>Itinéraire vers les composants de plusieurs assemblages

Utilisez `AdditionalAssemblies` le paramètre pour spécifier des assemblages supplémentaires pour le composant à considérer lors de la `Router` recherche de composants routables. Des assemblages spécifiés sont `AppAssembly`considérés en plus de l’assemblage spécifié. Dans l’exemple `Component1` suivant, est un composant routable défini dans une bibliothèque de classe référencée. L’exemple suivant `AdditionalAssemblies` se traduit `Component1`par un soutien de routage pour :

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Paramètres d’itinéraire

Le routeur utilise les paramètres d’itinéraire pour remplir les paramètres de composant correspondants du même nom (insensible au cas) :

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Les paramètres facultatifs ne sont pas pris en charge. Deux `@page` directives sont appliquées dans l’exemple précédent. Le premier permet la navigation sur le composant sans paramètre. La `@page` deuxième directive `{text}` prend le paramètre d’itinéraire et attribue la valeur à la `Text` propriété.

## <a name="route-constraints"></a>Contraintes d’itinéraire

Une contrainte d’itinéraire applique l’appariement de type sur un segment d’itinéraire à un composant.

Dans l’exemple suivant, `Users` l’itinéraire vers le composant ne correspond que si :

* Un `Id` segment d’itinéraire est présent sur l’URL de la demande.
* Le `Id` segment est un`int`intégrant ( ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Les contraintes d’itinéraire indiquées dans le tableau suivant sont disponibles. Pour les contraintes d’itinéraire qui correspondent à la culture invariante, voir l’avertissement ci-dessous le tableau pour plus d’informations.

| Contrainte | Exemple           | Exemples de correspondances                                                                  | Invariant<br>culture<br>correspondance |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Non                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Oui                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Oui                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Oui                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Oui                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Non                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Oui                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Oui                              |

> [!WARNING]
> Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant. ces contraintes partent du principe que l’URL n’est pas localisable.

### <a name="routing-with-urls-that-contain-dots"></a>Routage avec des URL qui contiennent des points

Dans les applications Blazor Server, l’itinéraire par défaut `/` dans`@page "/"` *_Host.cshtml* est ( ). Une URL de demande`.`qui contient un point ( ) n’est pas égalée par l’itinéraire par défaut parce que l’URL semble demander un fichier. Une application Blazor renvoie une réponse *404 - Non trouvée* pour un fichier statique qui n’existe pas. Pour utiliser les itinéraires qui contiennent un point, configurez *_Host.cshtml* avec le modèle d’itinéraire suivant :

```cshtml
@page "/{**path}"
```

Le `"/{**path}"` modèle comprend :

* Syntaxe *fourre-tout* double-astérisque (`**`) pour capturer le chemin à`/`travers les limites multiples de dossier sans codage vers l’avant slashes ( ).
* `path`nom du paramètre d’itinéraire.

> [!NOTE]
> La syntaxe de`*`/`**`paramètre *fourre-tout* ( ) **n’est pas** prise en charge dans les composants Razor (*.razor*).

Pour plus d’informations, consultez <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Composant NavLink

Utilisez `NavLink` un composant à la place`<a>`d’éléments hyperliens HTML ( ) lors de la création de liaisons de navigation. Un `NavLink` composant se `<a>` comporte comme un élément, `active` sauf qu’il bascule une classe CSS en fonction de si son `href` correspond à l’URL actuelle. La `active` classe aide un utilisateur à comprendre quelle page est la page active parmi les liens de navigation affichés.

Le `NavMenu` composant suivant crée une barre de navigation `NavLink` [Bootstrap](https://getbootstrap.com/docs/) qui démontre comment utiliser les composants :

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Il existe `NavLinkMatch` deux options que `Match` vous pouvez `<NavLink>` attribuer à l’attribut de l’élément :

* `NavLinkMatch.All`&ndash; Le `NavLink` est actif quand il correspond à l’ENSEMBLE de l’URL actuelle.
* `NavLinkMatch.Prefix`(*par défaut*) &ndash; Le `NavLink` est actif quand il correspond à n’importe quel préfixe de l’URL actuelle.

Dans l’exemple précédent, la Maison correspond à `NavLink` `href=""` l’URL à domicile et ne reçoit `active` la `https://localhost:5001/`classe CSS que sur l’URL de trajectoire de base par défaut de l’application (par exemple, ). Le `NavLink` second `active` reçoit la classe lorsque l’utilisateur visite n’importe quelle URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment`).

D’autres attributs de `NavLink` composants sont transmis à l’étiquette d’ancrage rendu. Dans l’exemple `NavLink` suivant, `target` le composant comprend l’attribut :

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

La balisage HTML suivante est rendue :

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Aides d’état de navigation et d’URI

Utilisez-le <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les ITI et la navigation dans le code C. `NavigationManager`fournit l’événement et les méthodes indiquées dans le tableau suivant.

| Membre | Description |
| ------ | ----------- |
| Uri | Obtient l’URI absolue actuelle. |
| BaseUri | Obtient la base URI (avec une barre oblique de fuite) qui peut être prépendi à des chemins relatifs URI pour produire une URI absolue. En `BaseUri` règle générale, `href` correspond à l’attribut sur l’élément `<base>` du document dans *wwwroot/index.html* (WebAssembly)Blazor ou *Pages/_Host.cshtml* (Serveur).Blazor |
| NaviguerTo | Navigue vers l’URI spécifiée. Si `forceLoad` `true`c’est :<ul><li>Le routage côté client est contourné.</li><li>Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit géré ou non par le routeur côté client.</li></ul> |
| Emplacement Modifié | Un événement qui s’agite lorsque l’emplacement de la navigation a changé. |
| ToAbsoluteUri | Convertit un URI relatif en URI absolu. |
| <span style="word-break:normal;word-wrap:normal">ÀBaseRelativePath</span> | Compte tenu d’une base URI (par `GetBaseUri`exemple, un URI précédemment retourné par ), convertit un URI absolu en un URI par rapport au préfixe de base URI. |

Le composant suivant navigue vers `Counter` le composant de l’application lorsque le bouton est sélectionné :

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Le composant suivant gère un événement modifié d’emplacement. La `HandleLocationChanged` méthode est décomookée lorsqu’elle `Dispose` est appelée par le cadre. Le fait de débrancher la méthode permet la collecte des ordures du composant.

```razor
@implement IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fournit les informations suivantes sur l’événement :

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; L’URL du nouvel emplacement.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Si `true` Blazor , intercepté la navigation à partir du navigateur. Si `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) a causé la navigation de se produire.

Pour plus d’informations <xref:blazor/lifecycle#component-disposal-with-idisposable>sur l’élimination des composants, voir .
