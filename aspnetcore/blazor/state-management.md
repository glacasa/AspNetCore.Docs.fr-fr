---
title: ASP.NET gestion Blazor de l’État de base
author: guardrex
description: Découvrez comment maintenir Blazor l’état dans les applications Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218867"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>ASP.NET gestion Blazor de l’État de base

Par [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorServer est un cadre d’application majestueux. La plupart du temps, l’application maintient une connexion continue au serveur. L’état de l’utilisateur est maintenu dans la mémoire du serveur dans un *circuit*. 

Voici quelques exemples d’état tenu pour le circuit d’un utilisateur :

* L’interface utilisateur&mdash;rendue la hiérarchie des instances de composants et leur sortie de rendu la plus récente.
* Les valeurs de tous les champs et propriétés dans les instances de composants.
* Données détenues dans des cas de service [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.

> [!NOTE]
> Cet article traite Blazor de la persistance de l’état dans les applications Server. BlazorLes applications WebAssembly peuvent tirer parti de la persistance de [l’état côté client dans le navigateur,](#client-side-in-the-browser) mais nécessitent des solutions personnalisées ou des paquets tiers au-delà de la portée de cet article.

## <a name="opno-locblazor-circuits"></a>BlazorCircuits

Si un utilisateur subit une perte Blazor de connexion réseau temporaire, tente de reconnecter l’utilisateur à son circuit d’origine afin qu’il puisse continuer à utiliser l’application. Cependant, il n’est pas toujours possible de reconnecter un utilisateur à son circuit d’origine à la mémoire du serveur :

* Le serveur ne peut pas conserver un circuit déconnecté pour toujours. Le serveur doit libérer un circuit déconnecté après un délai d’attente ou lorsque le serveur est sous pression de mémoire.
* Dans les environnements de déploiement multiservateurs et équilibrés, toutes les demandes de traitement de serveur peuvent devenir indisponibles à tout moment. Les serveurs individuels peuvent échouer ou être automatiquement supprimés lorsqu’ils ne sont plus tenus de traiter le volume global des demandes. Le serveur d’origine peut ne pas être disponible lorsque l’utilisateur tente de se reconnecter.
* L’utilisateur peut fermer et rouvrir son navigateur ou recharger la page, qui supprime tout état contenu dans la mémoire du navigateur. Par exemple, les valeurs définies via les appels interop JavaScript sont perdues.

Lorsqu’un utilisateur ne peut pas être reconnecté à son circuit d’origine, l’utilisateur reçoit un nouveau circuit avec un état vide. Cela équivaut à la fermeture et à la réouverture d’une application de bureau.

## <a name="preserve-state-across-circuits"></a>Préserver l’état à travers les circuits

Dans certains scénarios, il est souhaitable de préserver l’état à travers les circuits. Une application peut conserver des données importantes pour un utilisateur si :

* Le serveur web devient indisponible.
* Le navigateur de l’utilisateur est contraint de démarrer un nouveau circuit avec un nouveau serveur web.

En général, le maintien de l’état entre les circuits s’applique aux scénarios où les utilisateurs créent activement des données, et non pas simplement à lire des données qui existent déjà.

Pour préserver l’état au-delà d’un seul circuit, *ne vous contentez pas de stocker les données dans la mémoire du serveur*. L’application doit persister les données à un autre emplacement de stockage. La persistance de&mdash;l’État n’est pas automatique, vous devez prendre des mesures lors du développement de l’application pour implémenter la persistance des données étatiques.

La persistance des données n’est généralement nécessaire que pour l’état de grande valeur que les utilisateurs ont dépensé des efforts pour créer. Dans les exemples suivants, l’état persistant permet d’économiser du temps ou d’aide dans les activités commerciales :

* Formulaire Web &ndash; Multistep Il est long pour un utilisateur de saisir à nouveau des données pour plusieurs étapes terminées d’un processus multistep si leur état est perdu. Un utilisateur perd de l’état dans ce scénario s’il s’éloigne de la forme multistep et retourne à la forme plus tard.
* Panier &ndash; d’achat Tout élément commercialement important d’une application qui représente des revenus potentiels peut être maintenu. Un utilisateur qui perd son état, et donc son panier d’achat, peut acheter moins de produits ou de services lorsqu’il reviendra sur le site plus tard.

Il n’est généralement pas nécessaire de préserver l’état facilement recréé, tel que le nom d’utilisateur entré dans un dialogue de connexion qui n’a pas été soumis.

> [!IMPORTANT]
> Une application ne peut persister *l’état de l’application*. Les IG ne peuvent pas être persistées, comme les instances des composants et leurs arbres de rendu. Les composants et les arbres de rendu ne sont généralement pas sérialisables. Pour persister quelque chose de similaire à l’état d’interface utilisateur, tels que les nœuds élargis d’un TreeView, l’application doit avoir un code personnalisé pour modéliser le comportement comme état d’application sérialisable.

## <a name="where-to-persist-state"></a>Où persister l’état

Trois emplacements communs existent pour Blazor l’état persistant dans une application Server. Chaque approche est la mieux adaptée à différents scénarios et comporte des mises en garde différentes :

* [Côté serveur dans une base de données](#server-side-in-a-database)
* [URL](#url)
* [Côté client dans le navigateur](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Côté serveur dans une base de données

Pour la persistance permanente des données ou pour toute donnée qui doit s’étendre sur plusieurs utilisateurs ou appareils, une base de données indépendante côté serveur est presque certainement le meilleur choix. Options disponibles :

* Base de données SQL relationnelle
* stockage clé-valeur
* Magasin Blob
* Magasin de table

Une fois que les données sont enregistrées dans la base de données, un nouveau circuit peut être lancé par un utilisateur à tout moment. Les données de l’utilisateur sont conservées et disponibles dans n’importe quel nouveau circuit.

Pour plus d’informations sur les options de stockage de données Azure, consultez les [bases de données Azure Storage Documentation](/azure/storage/) et [Azure Databases](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Pour les données transitoires représentant l’état de navigation, modéliser les données dans le cadre de l’URL. Voici quelques exemples d’état modélisé dans l’URL :

* L’id d’une entité vue.
* Le numéro de page actuel dans une grille bipée.

Le contenu de la barre d’adresse du navigateur est conservé :

* Si l’utilisateur recharge manuellement la page.
* Si le serveur&mdash;Web devient indisponible, l’utilisateur est contraint de recharger la page afin de se connecter à un autre serveur.

Pour plus d’informations `@page` sur la <xref:blazor/routing>définition des modèles d’URL avec la directive, voir .

### <a name="client-side-in-the-browser"></a>Côté client dans le navigateur

Pour les données transitoires que l’utilisateur crée activement, `localStorage` un `sessionStorage` magasin d’accompagnement commun est celui du navigateur et des collections. L’application n’est pas nécessaire pour gérer ou effacer l’état stocké si le circuit est abandonné, ce qui est un avantage sur le stockage côté serveur.

> [!NOTE]
> "Client-side" dans cette section se réfère à des scénarios côté client dans le navigateur, pas le [ Blazor modèle d’hébergement WebAssembly](xref:blazor/hosting-models#blazor-webassembly). `localStorage`et `sessionStorage` peut être Blazor utilisé dans les applications WebAssembly, mais seulement en écrivant du code personnalisé ou en utilisant un paquet de 3ème partie.

`localStorage`et `sessionStorage` diffèrent comme suit:

* `localStorage`est étendue au navigateur de l’utilisateur. Si l’utilisateur recharge la page ou ferme et rouvre le navigateur, l’état persiste. Si l’utilisateur ouvre plusieurs onglets de navigateur, l’état est partagé sur les onglets. Les données `localStorage` persistent jusqu’à ce qu’elles soient explicitement effacées.
* `sessionStorage`est étendue à l’onglet navigateur de l’utilisateur. Si l’utilisateur recharge l’onglet, l’état persiste. Si l’utilisateur ferme l’onglet ou le navigateur, l’état est perdu. Si l’utilisateur ouvre plusieurs onglets de navigateur, chaque onglet a sa propre version indépendante des données.

En `sessionStorage` général, est plus sûr à utiliser. `sessionStorage`évite le risque qu’un utilisateur ouvre plusieurs onglets et rencontre ce qui suit :

* Bugs dans le stockage de l’état à travers les onglets.
* Comportement confus quand un onglet surwrite l’état d’autres onglets.

`localStorage`est le meilleur choix si l’application doit persister état à travers la fermeture et la réouverture du navigateur.

Mises en garde pour l’utilisation du stockage du navigateur :

* Semblable à l’utilisation d’une base de données côté serveur, le chargement et l’enregistrement des données sont asynchrones.
* Contrairement à une base de données côté serveur, le stockage n’est pas disponible pendant le préramanement parce que la page demandée n’existe pas dans le navigateur pendant l’étape de pré-démarrage.
* Le stockage de quelques kilooctets de Blazor données est raisonnable pour persister pour les applications Server. Au-delà de quelques kilooctets, vous devez tenir compte des implications de performance parce que les données sont chargées et enregistrées sur l’ensemble du réseau.
* Les utilisateurs peuvent afficher ou falsifier les données. ASP.NET [la protection des données de base](xref:security/data-protection/introduction) peut atténuer le risque.

## <a name="third-party-browser-storage-solutions"></a>Solutions de stockage de navigateurs tiers

Les forfaits NuGet tiers fournissent `localStorage` des `sessionStorage`API pour travailler avec et .

Il vaut la peine d’envisager de choisir un package qui utilise de manière transparente ASP.NET [la protection des données](xref:security/data-protection/introduction)de Core . ASP.NET Core Data Protection crypte les données stockées et réduit le risque potentiel de falsification des données stockées. Si les données Sérialisées JSON sont stockées en texte clair, les utilisateurs peuvent voir les données à l’aide d’outils de développeur de navigateurs et modifier également les données stockées. La sécurisation des données n’est pas toujours un problème parce que les données peuvent être de nature triviale. Par exemple, la lecture ou la modification de la couleur stockée d’un élément d’interface utilisateur n’est pas un risque de sécurité important pour l’utilisateur ou l’organisation. Évitez de permettre aux utilisateurs d’inspecter ou de falsifier des *données sensibles.*

## <a name="protected-browser-storage-experimental-package"></a>Paquet expérimental de stockage de navigateur protégé

Un exemple d’un paquet NuGet `localStorage` `sessionStorage` qui fournit la protection [des données](xref:security/data-protection/introduction) pour et est [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`est un paquet expérimental non soutenu qui ne convient pas à l’utilisation de la production en ce moment.

### <a name="installation"></a>Installation

Pour installer `Microsoft.AspNetCore.ProtectedBrowserStorage` le paquet :

1. Dans Blazor le projet d’application Server, ajoutez une référence de paquet à [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. Dans le HTML de haut niveau (par exemple, dans le fichier *Pages/_Host.cshtml* dans le modèle de projet par défaut), ajoutez l’étiquette suivante `<script>` :

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Dans `Startup.ConfigureServices` la méthode, appelez `AddProtectedBrowserStorage` à ajouter `localStorage` et `sessionStorage` les services à la collecte de service :

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Enregistrer et charger des données dans un composant

Dans tout composant qui nécessite le chargement [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) ou l’enregistrement des données au stockage du navigateur, utilisez-le pour injecter un exemple de l’un ou l’autre des éléments suivants :

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Le choix dépend du magasin de support que vous souhaitez utiliser. Dans l’exemple `sessionStorage` suivant, est utilisé:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

L’instruction `@using` peut être placée dans un fichier *_Imports.razor* au lieu de dans le composant. L’utilisation du fichier *_Imports.razor* met l’espace de nom à la disposition de plus grands segments de l’application ou de l’application entière.

Pour maintenir `_currentCount` la `Counter` valeur de la composante `IncrementCount` du modèle `ProtectedSessionStore.SetAsync`de projet, modifiez la méthode à utiliser :

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

Dans les applications plus grandes et plus réalistes, le stockage de champs individuels est un scénario peu probable. Les applications sont plus susceptibles de stocker des objets modèles entiers qui incluent un état complexe. `ProtectedSessionStore`sérialis et désélise automatiquement les données JSON.

Dans l’exemple de `_currentCount` code précédent, les données sont stockées comme `sessionStorage['count']` dans le navigateur de l’utilisateur. Les données ne sont pas stockées en texte clair mais sont plutôt protégées à l’aide de ASP.NET [protection des données](xref:security/data-protection/introduction)de Core . Les données chiffrées peuvent être vues si `sessionStorage['count']` elles sont évaluées dans la console de développeur du navigateur.

Pour récupérer `_currentCount` les données si `Counter` l’utilisateur retourne au composant plus tard (y compris s’ils sont sur un circuit entièrement nouveau), utilisez `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Si les paramètres du composant incluent `ProtectedSessionStore.GetAsync` l’état de `OnParametersSetAsync`navigation, appelez et attribuez le résultat dans , pas `OnInitializedAsync`. `OnInitializedAsync`n’est appelé qu’une seule fois lorsque le composant est d’abord instantané. `OnInitializedAsync`n’est pas appelé à nouveau plus tard si l’utilisateur navigue vers une URL différente tout en restant sur la même page. Pour plus d’informations, consultez <xref:blazor/lifecycle>.

> [!WARNING]
> Les exemples dans cette section ne fonctionnent que si le serveur n’a pas de prérendering activé. Avec le prerendering activé, une erreur est générée similaire à :
>
> > Les appels interop JavaScript ne peuvent pas être émis pour le moment. C’est parce que le composant est pré-rendu.
>
> Soit désactiver le prerendering ou ajouter du code supplémentaire pour travailler avec le prerendering. Pour en savoir plus sur l’écriture de code qui fonctionne avec le prerendering, consultez la section [de précommandage Handle.](#handle-prerendering)

### <a name="handle-the-loading-state"></a>Gérer l’état de chargement

Étant donné que le stockage du navigateur est asynchrone (accessible sur une connexion réseau), il ya toujours une période de temps avant que les données sont chargées et disponibles pour une utilisation par un composant. Pour les meilleurs résultats, rendre un message d’état de chargement pendant que le chargement est en cours au lieu d’afficher des données vierges ou par défaut.

Une approche consiste à déterminer `null` si les données sont (toujours en charge) ou non. Dans le `Counter` composant par défaut, `int`le nombre est tenu dans un . Rendre `_currentCount` l’annulation en ajoutant`?`un point`int`d’interrogation ( ) au type ( ):

```csharp
private int? _currentCount;
```

Au lieu d’afficher sans condition le bouton de comptage et **d’incrément,** choisissez d’afficher ces éléments uniquement si les données sont chargées :

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Gérer le prerendering

Pendant le précommandage :

* Il n’existe pas de connexion interactive au navigateur de l’utilisateur.
* Le navigateur n’a pas encore de page dans laquelle il peut exécuter le code JavaScript.

`localStorage`ou `sessionStorage` ne sont pas disponibles pendant le pré-programme. Si le composant tente d’interagir avec le stockage, une erreur est générée de manière similaire à :

> Les appels interop JavaScript ne peuvent pas être émis pour le moment. C’est parce que le composant est pré-rendu.

Une façon de résoudre l’erreur est de désactiver le prerendering. C’est généralement le meilleur choix si l’application fait un usage intensif du stockage basé sur le navigateur. Prerendering ajoute de la complexité et ne profite pas à l’application `localStorage` `sessionStorage` parce que l’application ne peut pas pré-rôder tout contenu utile jusqu’à ce que ou sont disponibles.

Pour désactiver le prerendering, ouvrez le fichier *Pages/_Host.cshtml* et modifiez le `render-mode` [composant Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) pour <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.

Prerendering peut être utile pour d’autres pages qui n’utilisent `localStorage` pas ou `sessionStorage`. Pour garder le prerendering activé, reportez l’opération de chargement jusqu’à ce que le navigateur soit connecté au circuit. Ce qui suit est un exemple pour stocker une valeur de comptoir :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Tenez compte de la préservation de l’État à un endroit commun

Si de nombreux composants s’appuient sur le stockage par navigateur, la ré-implémentation du code fournisseur d’État crée plusieurs fois la duplication du code. Une option pour éviter la duplication de code est de créer un *composant parent fournisseur d’état* qui encapsule la logique de fournisseur d’état. Les composants pour enfants peuvent fonctionner avec des données persistantes sans tenir compte du mécanisme de persistance de l’État.

Dans l’exemple `CounterStateProvider` suivant d’un composant, les données de compteur sont persistées :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Le `CounterStateProvider` composant gère la phase de chargement en ne rendant pas son contenu enfant jusqu’à ce que le chargement soit terminé.

Pour utiliser `CounterStateProvider` le composant, enveloppez une instance du composant autour de tout autre composant qui nécessite l’accès à l’état de comptoir. Pour rendre l’état accessible à tous les `CounterStateProvider` composants `Router` d’une application, enveloppez le composant autour du `App` composant *(App.razor*) :

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Les composants enveloppés reçoivent et peuvent modifier l’état de comptoir persistant. Le `Counter` composant suivant met en œuvre le modèle :

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Le composant précédent n’est `ProtectedBrowserStorage`pas nécessaire pour interagir avec, ni traiter d’une phase de «chargement».

Pour faire face à la prérendering comme décrit précédemment, `CounterStateProvider` peut être modifié de sorte que tous les composants qui consomment les données de compteur fonctionnent automatiquement avec le prerendering. Voir la section [De pré-appel de poignée](#handle-prerendering) pour plus de détails.

En général, le modèle *de composante parent du fournisseur d’État* est recommandé :

* Pour consommer l’état dans beaucoup d’autres composants.
* S’ll n’y a qu’un seul objet de haut niveau à persister.

Pour persister de nombreux objets d’état différents et consommer différents sous-ensembles d’objets dans différents endroits, il est préférable d’éviter de manipuler le chargement et l’économie de l’État dans le monde entier.
