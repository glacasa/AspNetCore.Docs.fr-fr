---
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---
# <a name="aspnet-core-blazor-routing"></a>Routage de ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex)

Découvrez comment acheminer les demandes et comment utiliser le `NavLink` composant pour créer des liens de navigation dans les Blazor applications.

## <a name="aspnet-core-endpoint-routing-integration"></a>Intégration du routage du point de terminaison ASP.NET Core

BlazorLe serveur est intégré à [ASP.net Core routage des points de terminaison](xref:fundamentals/routing). Une application ASP.NET Core est configurée pour accepter les connexions entrantes pour les composants interactifs avec `MapBlazorHub` dans `Startup.Configure` :

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

La configuration la plus courante consiste à acheminer toutes les demandes vers une Razor page, qui joue le rôle d’hôte pour la partie côté serveur de l' Blazor application serveur. Par Convention, la page *hôte* est généralement nommée *_Host. cshtml*. L’itinéraire spécifié dans le fichier hôte est appelé *itinéraire de secours* , car il fonctionne avec une priorité basse dans la correspondance d’itinéraire. L’itinéraire de secours est pris en compte lorsque les autres itinéraires ne correspondent pas. Cela permet à l’application d’utiliser d’autres contrôleurs et pages sans interférer avec l' Blazor application serveur.

## <a name="route-templates"></a>Modèles de routage

Le `Router` composant active le routage vers chaque composant avec un itinéraire spécifié. Le `Router` composant apparaît dans le fichier *app. Razor* :

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

Lorsqu’un fichier *. Razor* avec une `@page` directive est compilé, la classe générée est fournie en <xref:Microsoft.AspNetCore.Components.RouteAttribute> spécifiant le modèle de routage.

Au moment de l’exécution, le `RouteView` composant :

* Reçoit du avec les `RouteData` `Router` paramètres souhaités.
* Restitue le composant spécifié avec sa disposition (ou une disposition par défaut facultative) à l’aide des paramètres spécifiés.

Vous pouvez éventuellement spécifier un `DefaultLayout` paramètre avec une classe de disposition à utiliser pour les composants qui ne spécifient pas de disposition. Les modèles par défaut Blazor spécifient le `MainLayout` composant. *MainLayout. Razor* se trouve dans le dossier *partagé* du projet de modèle. Pour plus d’informations sur les mises en page, consultez <xref:blazor/layouts> .

Plusieurs modèles de routage peuvent être appliqués à un composant. Le composant suivant répond aux demandes pour `/BlazorRoute` et `/DifferentBlazorRoute` :

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Pour que les URL soient correctement résolues, l’application doit inclure une `<base>` balise dans son fichier *wwwroot/index.html* ( Blazor webassembly) ou le fichier *pages/_Host. cshtml* ( Blazor serveur) avec le chemin d’accès de base de l’application spécifié dans l' `href` attribut ( `<base href="/">` ). Pour plus d'informations, consultez <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fournir du contenu personnalisé lorsque le contenu est introuvable

Le `Router` composant permet à l’application de spécifier du contenu personnalisé si le contenu est introuvable pour l’itinéraire demandé.

Dans le fichier *app. Razor* , définissez le contenu personnalisé dans le `NotFound` paramètre de modèle du `Router` composant :

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

Le contenu des `<NotFound>` balises peut inclure des éléments arbitraires, tels que d’autres composants interactifs. Pour appliquer une disposition par défaut au `NotFound` contenu, consultez <xref:blazor/layouts> .

## <a name="route-to-components-from-multiple-assemblies"></a>Acheminer vers des composants à partir de plusieurs assemblys

Utilisez le `AdditionalAssemblies` paramètre pour spécifier des assemblys supplémentaires `Router` que le composant doit prendre en compte lors de la recherche de composants routables. Les assemblys spécifiés sont pris en compte en plus de l' `AppAssembly` assembly spécifié. Dans l’exemple suivant, `Component1` est un composant routable défini dans une bibliothèque de classes référencée. L' `AdditionalAssemblies` exemple suivant entraîne la prise en charge du routage pour `Component1` :

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Paramètres d’itinéraire

Le routeur utilise des paramètres de routage pour remplir les paramètres de composant correspondants avec le même nom (sans respect de la casse) :

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

Les paramètres facultatifs ne sont pas pris en charge. Deux `@page` directives sont appliquées dans l’exemple précédent. La première permet de naviguer jusqu’au composant sans paramètre. La deuxième `@page` directive prend le `{text}` paramètre d’itinéraire et assigne la valeur à la `Text` propriété.

## <a name="route-constraints"></a>Contraintes d’itinéraire

Une contrainte d’itinéraire applique la correspondance de type sur un segment de routage à un composant.

Dans l’exemple suivant, l’itinéraire vers le `Users` composant correspond uniquement à si :

* Un `Id` segment de routage est présent sur l’URL de la demande.
* Le `Id` segment est un entier ( `int` ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Les contraintes de routage indiquées dans le tableau suivant sont disponibles. Pour plus d’informations sur les contraintes d’itinéraire qui correspondent à la culture dite indifférente, consultez l’avertissement sous le tableau.

| Contrainte | Exemple           | Exemples de correspondances                                                                  | Invariant<br>culture<br>correspondance |
| ---
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

----- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

--------- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---------------------------------------- | :---titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Non | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Oui | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Oui | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Oui | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Oui | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Non | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Oui | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Oui |

> [!WARNING]
> Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant. ces contraintes partent du principe que l’URL n’est pas localisable.

### <a name="routing-with-urls-that-contain-dots"></a>Routage avec des URL qui contiennent des points

Dans Blazor les applications serveur, l’itinéraire par défaut dans *_Host. cshtml* est `/` ( `@page "/"` ). Une URL de demande qui contient un point ( `.` ) ne correspond pas à l’itinéraire par défaut, car l’URL semble demander un fichier. Une Blazor application renvoie une réponse *404-introuvable* pour un fichier statique qui n’existe pas. Pour utiliser des itinéraires qui contiennent un point, configurez *_Host. cshtml* avec le modèle d’itinéraire suivant :

```cshtml
@page "/{**path}"
```

Le `"/{**path}"` modèle comprend les éléments suivants :

* Double-astérisque syntaxe *catch-all* ( `**` ) pour capturer le chemin d’accès dans plusieurs limites de dossiers sans encodage des barres obliques ( `/` ).
* `path`nom du paramètre d’itinéraire.

> [!NOTE]
> La syntaxe de paramètre *catch-all* ( `*` / `**` ) n’est **pas** prise en charge dans les Razor composants (*. Razor*).

Pour plus d'informations, consultez <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Composant NavLink

Utilisez un `NavLink` composant à la place des éléments Hyperlink html ( `<a>` ) lors de la création de liens de navigation. Un `NavLink` composant se comporte comme un `<a>` élément, sauf qu’il fait basculer une `active` classe CSS selon que son `href` correspond à l’URL actuelle. La `active` classe permet à un utilisateur de comprendre quelle page est la page active parmi les liens de navigation affichés.

Le `NavMenu` composant suivant crée une barre de navigation de [démarrage](https://getbootstrap.com/docs/) qui montre comment utiliser des `NavLink` composants :

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Il existe deux `NavLinkMatch` options que vous pouvez assigner à l' `Match` attribut de l' `<NavLink>` élément :

* `NavLinkMatch.All`&ndash; `NavLink` Est actif lorsqu’il correspond à la totalité de l’URL actuelle.
* `NavLinkMatch.Prefix`(*par défaut*) &ndash; `NavLink`Est actif lorsqu’il correspond à n’importe quel préfixe de l’URL actuelle.

Dans l’exemple précédent, la page `NavLink` `href=""` d’hébergement correspond à l’URL de base et reçoit uniquement la `active` classe CSS à l’URL du chemin de base par défaut de l’application (par exemple, `https://localhost:5001/` ). Le deuxième `NavLink` reçoit la `active` classe lorsque l’utilisateur visite une URL avec un `MyComponent` préfixe (par exemple, `https://localhost:5001/MyComponent` et `https://localhost:5001/MyComponent/AnotherSegment` ).

`NavLink`Les attributs de composant supplémentaires sont passés à la balise d’ancrage rendue. Dans l’exemple suivant, le `NavLink` composant comprend l' `target` attribut :

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Le balisage HTML suivant est rendu :

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Aide des URI et de l’état de navigation

Utilisez <xref:Microsoft.AspNetCore.Components.NavigationManager> pour travailler avec les URI et la navigation dans le code C#. `NavigationManager`fournit l’événement et les méthodes répertoriées dans le tableau suivant.

| Membre | Description |
| ---
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

--- | titre de la--- : 'ASP.NET Core du Blazor routage’auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

-
titre : « ASP.NET Core Blazor routage » auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID : 

------ | | URI | Obtient l’URI absolu actuel. | | BaseUri | Obtient l’URI de base (avec une barre oblique finale) qui peut être ajouté aux chemins d’accès URI relatifs pour produire un URI absolu. En général, `BaseUri` correspond à l' `href` attribut sur l’élément du document `<base>` dans *wwwroot/index.html* ( Blazor webassembly) ou *pages/_Host. cshtml* ( Blazor Server). | | NavigateTo | Navigue vers l’URI spécifié. Si `forceLoad` est `true` :<ul><li>Le routage côté client est contourné.</li><li>Le navigateur est obligé de charger la nouvelle page à partir du serveur, que l’URI soit normalement géré ou non par le routeur côté client.</li></ul> | | LocationChanged | Événement qui se déclenche lorsque l’emplacement de navigation a changé. | | ToAbsoluteUri | Convertit un URI relatif en URI absolu. | | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | À partir d’un URI de base (par exemple, un URI précédemment retourné par `GetBaseUri` ), convertit un URI absolu en un URI relatif au préfixe URI de base. |

Le composant suivant accède au composant de l’application `Counter` lorsque le bouton est sélectionné :

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

Le composant suivant gère un événement de modification d’emplacement. La `HandleLocationChanged` méthode est déconnectée lorsque `Dispose` est appelé par l’infrastructure. Le déraccordement de la méthode permet de garbage collection du composant.

```razor
@implements IDisposable
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

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>fournit les informations suivantes sur l’événement :

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash;URL du nouvel emplacement.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash;Si `true` , a Blazor intercepté la navigation à partir du navigateur. Si `false` la valeur est, [NavigationManager. NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) a entraîné la navigation.

Pour plus d’informations sur la suppression de composants, consultez <xref:blazor/lifecycle#component-disposal-with-idisposable> .
