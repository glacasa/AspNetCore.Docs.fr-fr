---
title: Sécurisez Blazor les applications Core Server ASP.NET
author: guardrex
description: Découvrez comment atténuer les Blazor menaces de sécurité pour les applications Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626026"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Sécurisez les applications Core Blazor Server ASP.NET

Par [Javier Calvarro Nelson](https://github.com/javiercn)

Les applications Blazor Server adoptent un modèle de traitement de données *majestueux,* où le serveur et le client entretiennent une relation de longue durée. L’état persistant est maintenu par un [circuit,](xref:blazor/state-management)qui peut s’étendre sur des connexions qui sont également potentiellement à long terme.

Lorsqu’un utilisateur visite un site Blazor Server, le serveur crée un circuit dans la mémoire du serveur. Le circuit indique au navigateur quel contenu rendre et répond aux événements, comme lorsque l’utilisateur sélectionne un bouton dans l’interface utilisateur. Pour effectuer ces actions, un circuit invoque les fonctions JavaScript dans le navigateur de l’utilisateur et les méthodes .NET sur le serveur. Cette interaction bidirectionnelle basée sur JavaScript est appelée [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).

Étant donné que JS interop se produit sur Internet et que le client utilise un navigateur distant, les applications Blazor Server partagent la plupart des problèmes de sécurité de l’application Web. Ce sujet décrit les menaces courantes pour les applications Blazor Server et fournit des conseils d’atténuation des menaces axés sur les applications orientées vers Internet.

Dans les environnements restreints, comme à l’intérieur des réseaux d’entreprise ou des intranets, certaines des directives d’atténuation sont les plus :

* Ne s’applique pas dans l’environnement restreint.
* Ne vaut pas le coût de mise en œuvre parce que le risque pour la sécurité est faible dans un environnement limité.

## <a name="blazor-server-project-template"></a>Modèle de projet Blazor Server

Le modèle de projet Blazor Server peut être configuré pour l’authentification lorsque le projet est créé.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Suivez les conseils Visual <xref:blazor/get-started> Studio dans l’article pour créer un nouveau projet Blazor Server avec un mécanisme d’authentification.

Après avoir choisi le modèle **Application serveur Blazor** dans la boîte de dialogue **Créer une application web ASP.NET Core.**, sélectionnez **Modifier** sous **Authentification**.

Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :

* **Pas d’authentification**
* **Comptes d’utilisateur individuels** &ndash; Les comptes d'utilisateur peuvent être stockés :
  * Dans l’application à l’aide du système [d’identité](xref:security/authentication/identity) d’ASP.NET Core.
  * Avec [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Comptes de travail ou d’école**
* **Authentification Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Suivez les directives visual <xref:blazor/get-started> studio Code dans l’article pour créer un nouveau projet Blazor Server avec un mécanisme d’authentification :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification                                                                 | Valeur `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Aucune authentification                                                                        | `None`                   |
| Individuel<br>Les utilisateurs stockés dans l’application avec l’identité ASP.NET Core.                        | `Individual`             |
| Individuel<br>Les utilisateurs stockés dans [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Comptes professionnels ou scolaires<br>Authentification d’organisation pour un seul abonné.            | `SingleOrg`              |
| Comptes professionnels ou scolaires<br>Authentification d’organisation pour plusieurs abonnés.           | `MultiOrg`               |
| Authentification Windows                                                                   | `Windows`                |

La commande crée un dossier nommé avec la valeur fournie pour l’espace réservé `{APP NAME}` et utilise le nom de dossier en tant que nom de l’application. Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Suivez le Visual Studio pour <xref:blazor/get-started> Mac guide dans l’article.

1. Sur la configuration de votre nouvelle étape **d’application serveur Blazor,** sélectionnez **l’authentification individuelle (in-app)** de **l’authentification** tomber.

1. L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET’identité de base.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Suivez les conseils CLI de <xref:blazor/get-started> base .NET dans l’article pour créer un nouveau projet Blazor Server avec un mécanisme d’authentification :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification                                                                 | Valeur `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Aucune authentification                                                                        | `None`                   |
| Individuel<br>Les utilisateurs stockés dans l’application avec l’identité ASP.NET Core.                        | `Individual`             |
| Individuel<br>Les utilisateurs stockés dans [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Comptes professionnels ou scolaires<br>Authentification d’organisation pour un seul abonné.            | `SingleOrg`              |
| Comptes professionnels ou scolaires<br>Authentification d’organisation pour plusieurs abonnés.           | `MultiOrg`               |
| Authentification Windows                                                                   | `Windows`                |

La commande crée un dossier nommé avec la valeur fournie pour l’espace réservé `{APP NAME}` et utilise le nom de dossier en tant que nom de l’application. Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Passez les jetons à une application Blazor Server

Authentifier l’application Blazor Server comme vous le feriez avec une application régulière Razor Pages ou MVC. Fournir et enregistrer les jetons au cookie d’authentification. Par exemple :

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Pour l’exemple de `Startup.ConfigureServices` code, y compris un exemple complet, voir les [jetons Passing à une application Blazor côté serveur](https://github.com/javiercn/blazor-server-aad-sample).

Définissez une classe à passer dans l’état initial de l’application avec les jetons d’accès et de rafraîchissement :

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Définissez un service de fournisseur de jetons **à portée** qui peut être utilisé dans l’application Blazor pour résoudre les jetons de DI :

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Dans `Startup.ConfigureServices`, ajouter des services pour:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Dans le fichier *_Host.cshtml,* créez et `InitialApplicationState` instancez et transmettez-le comme paramètre à l’application :

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

Dans `App` le composant (*App.razor*), résoudre le service et l’initialiser avec les données du paramètre :

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Dans le service qui fait une demande d’API sécurisée, injectez le fournisseur de jetons et récupérez le jeton pour appeler l’API :

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>Épuisement des ressources

L’épuisement des ressources peut se produire lorsqu’un client interagit avec le serveur et fait consommer des ressources excessives au serveur. La consommation excessive de ressources affecte principalement :

* [UC](#cpu)
* [Mémoire](#memory)
* [Connexions clientes](#client-connections)

Les attaques par déni de service (DoS) visent généralement à épuiser les ressources d’une application ou d’un serveur. Cependant, l’épuisement des ressources n’est pas nécessairement le résultat d’une attaque contre le système. Par exemple, les ressources limitées peuvent être épuisées en raison de la forte demande des utilisateurs. DoS est couvert plus loin dans la section [des attaques par déni de service (DoS).](#denial-of-service-dos-attacks)

Les ressources externes au cadre Blazor, telles que les bases de données et les poignées de fichiers (utilisées pour lire et écrire des fichiers), peuvent également faire l’expérience de l’épuisement des ressources. Pour plus d’informations, consultez <xref:performance/performance-best-practices>.

### <a name="cpu"></a>UC

L’épuisement du processeur peut se produire lorsqu’un ou plusieurs clients forcent le serveur à effectuer un travail intensif de processeur.

Par exemple, considérez une application Blazor Server qui calcule un *numéro Fibonnacci*. Un numéro Fibonnacci est produit à partir d’une séquence Fibonnacci, où chaque numéro de la séquence est la somme des deux nombres précédents. La quantité de travail nécessaire pour atteindre la réponse dépend de la durée de la séquence et de la taille de la valeur initiale. Si l’application ne limite pas la demande d’un client, les calculs à forte intensité de processeur peuvent dominer le temps du processeur et diminuer les performances d’autres tâches. La consommation excessive de ressources est une préoccupation de sécurité qui a une incidence sur la disponibilité.

L’épuisement du processeur est une préoccupation pour toutes les applications orientées vers le public. Dans les applications Web régulières, les demandes et les connexions s’affichent comme garantie, mais les applications Blazor Server ne fournissent pas les mêmes garanties. Les applications Blazor Server doivent inclure des contrôles et des limites appropriés avant d’effectuer un travail potentiellement intensif au processeur.

### <a name="memory"></a>Mémoire

L’épuisement de mémoire peut se produire quand un ou plusieurs clients forcent le serveur à consommer une grande quantité de mémoire.

Par exemple, considérez une application côté Serveur Blazor avec un composant qui accepte et affiche une liste d’éléments. Si l’application Blazor ne limite pas le nombre d’éléments autorisés ou le nombre d’éléments rendus au client, le traitement et le rendu à forte intensité de mémoire peuvent dominer la mémoire du serveur au point où les performances du serveur en souffrent. Le serveur peut s’écraser ou ralentir au point qu’il semble s’être écrasé.

Considérez le scénario suivant pour maintenir et afficher une liste d’éléments qui se rapportent à un scénario potentiel d’épuisement de mémoire sur le serveur :

* Les éléments `List<MyItem>` d’une propriété ou d’un champ utilisent la mémoire du serveur. Si l’application permet à la liste des éléments de croître sans limites, il ya un risque de la panne du serveur à court de mémoire. Manquer de mémoire provoque la fin de la session actuelle (crash) et toutes les sessions simultanées dans cette instance serveur reçoivent une exception hors mémoire. Pour éviter que ce scénario ne se produise, l’application doit utiliser une structure de données qui impose une limite d’élément aux utilisateurs simultanés.
* Si un système de mise à l’emploi n’est pas utilisé pour le rendu, le serveur utilise une mémoire supplémentaire pour les objets qui ne sont pas visibles dans l’interface utilisateur. Sans limite au nombre d’éléments, les exigences de mémoire peuvent épuiser la mémoire du serveur disponible. Pour éviter ce scénario, utilisez l’une des approches suivantes :
  * Utilisez des listes paginées lors du rendu.
  * N’affichez que les 100 à 1 000 premiers éléments et obligez l’utilisateur à entrer des critères de recherche pour trouver des éléments au-delà des éléments affichés.
  * Pour un scénario de rendu plus avancé, implémentez des listes ou des grilles qui prennent en charge *la virtualisation.* À l’aide de la virtualisation, les listes ne rendent qu’un sous-ensemble d’éléments actuellement visibles par l’utilisateur. Lorsque l’utilisateur interagit avec la barre de défilement dans l’interface utilisateur, le composant ne rend que les éléments requis pour l’affichage. Les articles qui ne sont pas actuellement nécessaires pour l’affichage peuvent être conservés dans le stockage secondaire, qui est l’approche idéale. Les objets non joués peuvent également être conservés dans la mémoire, ce qui est moins idéal.

Les applications Blazor Server offrent un modèle de programmation similaire à d’autres cadres d’interface utilisateur pour les applications étatiques, telles que WPF, Windows Forms ou Blazor WebAssembly. La principale différence est que dans plusieurs des cadres d’interface utilisateur la mémoire consommée par l’application appartient au client et affecte seulement ce client individuel. Par exemple, une application WebAssembly Blazor s’exécute entièrement sur le client et n’utilise que les ressources de mémoire client. Dans le scénario Blazor Server, la mémoire consommée par l’application appartient au serveur et est partagée entre les clients sur l’instance serveur.

Les exigences de mémoire côté serveur sont une considération pour toutes les applications Blazor Server. Cependant, la plupart des applications Web sont apatrides, et la mémoire utilisée lors du traitement d’une demande est publiée lorsque la réponse est retournée. Comme recommandation générale, ne permettez pas aux clients d’allouer une quantité illimitée de mémoire comme dans toute autre application côté serveur qui persiste les connexions client. La mémoire consommée par une application Blazor Server persiste plus longtemps qu’une seule demande.

> [!NOTE]
> Pendant le développement, un profileur peut être utilisé ou une trace capturée pour évaluer les demandes de mémoire des clients. Un profileur ou une trace ne capte pas la mémoire attribuée à un client spécifique. Pour capturer l’utilisation de mémoire d’un client spécifique pendant le développement, capturez un dépotoir et examinez la demande de mémoire de tous les objets enracinés sur le circuit d’un utilisateur.

### <a name="client-connections"></a>Connexions clientes

L’épuisement de connexion peut se produire quand un ou plusieurs clients ouvrent trop de connexions simultanées au serveur, empêchant d’autres clients d’établir de nouvelles connexions.

Les clients de Blazor établissent une connexion unique par session et gardent la connexion ouverte aussi longtemps que la fenêtre du navigateur est ouverte. Les exigences sur le serveur de maintenir toutes les connexions n’est pas spécifique aux applications Blazor. Compte tenu de la nature persistante des connexions et de la nature acuité des applications Blazor Server, l’épuisement de connexion est un plus grand risque pour la disponibilité de l’application.

Par défaut, il n’y a pas de limite au nombre de connexions par utilisateur pour une application Blazor Server. Si l’application nécessite une limite de connexion, prenez une ou plusieurs des approches suivantes :

* Exiger l’authentification, ce qui limite naturellement la capacité des utilisateurs non autorisés à se connecter à l’application. Pour que ce scénario soit efficace, les utilisateurs doivent être empêchés de fournir de nouveaux utilisateurs à volonté.
* Limitez le nombre de connexions par utilisateur. Limiter les connexions peut être accomplie par les approches suivantes. Faites preuve de prudence pour permettre aux utilisateurs légitimes d’accéder à l’application (par exemple, lorsqu’une limite de connexion est établie en fonction de l’adresse IP du client).
  * Au niveau de l’application :
    * Extéabilité de routage de point de terminaison.
    * Nécessitez une authentification pour se connecter à l’application et suivre les sessions actives par utilisateur.
    * Rejeter les nouvelles sessions en atteignant une limite.
    * Proxy WebSocket connexions à une application grâce à l’utilisation d’un proxy, tels que le [service SignalR Azure](/azure/azure-signalr/signalr-overview) qui multiplexe les connexions des clients à une application. Cela fournit une application avec une plus grande capacité de connexion qu’un seul client peut établir, empêchant un client d’épuiser les connexions au serveur.
  * Au niveau du serveur : Utilisez un proxy/passerelle en face de l’application. Par exemple, [Azure Front Door](/azure/frontdoor/front-door-overview) vous permet de définir, gérer et surveiller le routage global du trafic Web vers une application.

## <a name="denial-of-service-dos-attacks"></a>Attaques par déni de service (DoS)

Les attaques par déni de service (DoS) impliquent un client qui fait épuiser une ou plusieurs de ses ressources, ce qui rend l’application indisponible. Les applications Blazor Server comprennent certaines limites par défaut et s’appuient sur d’autres limites ASP.NET Core et SignalR pour se protéger contre les attaques DoS :

| Limite de l’application Blazor Server                            | Description | Default |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Nombre maximum de circuits déconnectés qu’un serveur donné tient en mémoire à la fois. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | La durée maximale d’un circuit déconnecté est conservée dans la mémoire avant d’être démolie. | 3 minutes |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Durée maximale du temps que le serveur attend avant de chronométrer une invocation asynchrone fonction JavaScript. | 1 minute |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Nombre maximum de lots de rendu non reconnus que le serveur garde en mémoire par circuit à un moment donné pour prendre en charge une reconnexion robuste. Après avoir atteint la limite, le serveur cesse de produire de nouveaux lots de rendu jusqu’à ce qu’un ou plusieurs lots aient été reconnus par le client. | 10 |


| Limite de base signalR et ASP.NET             | Description | Default |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Taille du message pour un message individuel. | 32 Ko |

## <a name="interactions-with-the-browser-client"></a>Interactions avec le navigateur (client)

Un client interagit avec le serveur par le biais de l’expédition de l’événement JS interop et rend l’achèvement. JS interop communication va dans les deux sens entre JavaScript et .NET:

* Les événements du navigateur sont expédiés du client au serveur d’une manière asynchrone.
* Le serveur répond asynchronement rerendering l’interface utilisateur si nécessaire.

### <a name="javascript-functions-invoked-from-net"></a>Fonctions JavaScript invoquées à partir de .NET

Pour les appels à partir de méthodes .NET à JavaScript:

* Toutes les invocations ont un délai d’attente <xref:System.OperationCanceledException> configurable après quoi ils échouent, retournant un à l’appelant.
  * Il ya un délai d’attente`CircuitOptions.JSInteropDefaultCallTimeout`par défaut pour les appels ( ) d’une minute. Pour configurer cette <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limite, voir .
  * Un jeton d’annulation peut être fourni pour contrôler l’annulation par appel. S’appuyer sur le délai d’attente d’appel par défaut dans la mesure du possible et en délai d’appel au client si un jeton d’annulation est fourni.
* Le résultat d’un appel JavaScript ne peut pas être fiable. Le Blazor client de l’application en cours d’exécution dans les recherches de navigateur pour la fonction JavaScript à invoquer. La fonction est invoquée, et soit le résultat ou une erreur est produit. Un client malveillant peut tenter de :
  * Causez un problème dans l’application en retournant une erreur de la fonction JavaScript.
  * Induire un comportement involontaire sur le serveur en retournant un résultat inattendu de la fonction JavaScript.

Prenez les précautions suivantes pour vous prémunir contre les scénarios précédents :

* Enveloppez les appels interop JS dans les instructions [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pour tenir compte des erreurs qui pourraient se produire pendant les invocations. Pour plus d’informations, consultez <xref:blazor/handle-errors#javascript-interop>.
* Valider les données retournées des invocations interop JS, y compris les messages d’erreur, avant de prendre des mesures.

### <a name="net-methods-invoked-from-the-browser"></a>.NET méthodes invoquées à partir du navigateur

Ne faites pas confiance aux appels de JavaScript vers des méthodes .NET. Lorsqu’une méthode .NET est exposée à JavaScript, réfléchissez à la façon dont la méthode .NET est invoquée :

* Traitez n’importe quelle méthode .NET exposée à JavaScript comme vous le feriez un point de terminaison public à l’application.
  * Valider l’entrée.
    * Assurez-vous que les valeurs sont dans les fourchettes attendues.
    * Assurez-vous que l’utilisateur a la permission d’effectuer l’action demandée.
  * N’allouez pas une quantité excessive de ressources dans le cadre de l’invocation de méthode .NET. Par exemple, effectuez des contrôles et placez des limites sur le processeur et l’utilisation de la mémoire.
  * Prenez en compte que les méthodes statiques et d’instance peuvent être exposées aux clients JavaScript. Évitez de partager l’état entre les sessions à moins que la conception exige le partage de l’état avec des contraintes appropriées.
    * Par exemple, `DotNetReference` les méthodes exposées à travers des objets qui sont créés à l’origine par injection de dépendance (DI), les objets doivent être enregistrés comme des objets de portée. Cela s’applique à Blazor tout service DI que l’application Server utilise.
    * Pour les méthodes statiques, évitez d’établir un état qui ne peut être étendue au client à moins que l’application ne partage explicitement la conception par état de tous les utilisateurs sur une instance de serveur.
  * Évitez de transmettre des données fournies par l’utilisateur dans les paramètres des appels JavaScript. Si l’adoption de données dans les paramètres est absolument nécessaire, assurez-vous que le code JavaScript gère le passage des données sans introduire les vulnérabilités [de script cross-site (XSS).](#cross-site-scripting-xss) Par exemple, n’écrivez pas de données fournies par l’utilisateur au modèle d’objet document (DOM) en définissant la `innerHTML` propriété d’un élément. Envisagez d’utiliser [la politique de sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) pour désactiver `eval` et d’autres primitifs JavaScript dangereux.
* Évitez d’implémenter l’envoi personnalisé d’invocations .NET en plus de la mise en œuvre de l’expédition du cadre. Exposer les méthodes .NET au navigateur est un Blazor scénario avancé, pas recommandé pour le développement général.

### <a name="events"></a>Événements

Les événements fournissent un Blazor point d’entrée à une application Server. Les mêmes règles pour la sauvegarde des paramètres dans Blazor les applications Web s’appliquent à la gestion d’événements dans les applications Server. Un client malveillant peut envoyer toutes les données qu’il souhaite envoyer comme charge utile pour un événement.

Par exemple :

* Un événement de `<select>` modification pour un pourrait envoyer une valeur qui n’est pas dans les options que l’application a présenté au client.
* Un `<input>` pourrait envoyer toutes les données de texte au serveur, en contournant la validation côté client.

L’application doit valider les données pour tout événement que l’application gère. Les Blazor [formulaires-cadres](xref:blazor/forms-validation) effectuent des validations de base. Si l’application utilise des composants de formulaires personnalisés, le code personnalisé doit être écrit pour valider les données de l’événement, le cas échéant.

BlazorLes événements du serveur sont asynchrones, de sorte que plusieurs événements peuvent être envoyés sur le serveur avant que l’application a le temps de réagir en produisant un nouveau rendu. Cela a des implications en matière de sécurité à prendre en considération. Limiter les actions des clients dans l’application doit être effectuée à l’intérieur des gestionnaires d’événements et ne pas dépendre de l’état de vue rendu en cours.

Considérez un composant de compteur qui devrait permettre à un utilisateur d’incrémenter un compteur un maximum de trois fois. Le bouton pour incrémenter le compteur est `count`conditionnellement basé sur la valeur de :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Un client peut envoyer une ou plusieurs incréments avant que le cadre ne produise un nouveau rendu de ce composant. Le résultat est `count` que le peut être incrémenté *plus de trois fois* par l’utilisateur parce que le bouton n’est pas supprimé par l’interface utilisateur assez rapidement. La bonne façon d’atteindre `count` la limite de trois incréments est indiquée dans l’exemple suivant :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

En ajoutant `if (count < 3) { ... }` le chèque à l’intérieur `count` du gestionnaire, la décision d’incrément est basée sur l’état actuel de l’application. La décision n’est pas fondée sur l’état de l’assurance-chômage comme elle l’était dans l’exemple précédent, qui pourrait être temporairement périmée.

### <a name="guard-against-multiple-dispatches"></a>Se prémunir contre les expéditions multiples

Si un rappel d’événements invoque une opération de longue durée asynchrone, comme aller chercher des données à partir d’un service externe ou d’une base de données, envisagez d’utiliser un garde. Le gardien peut empêcher l’utilisateur de faire la queue vers le haut de plusieurs opérations pendant que l’opération est en cours avec la rétroaction visuelle. Le code composant `isLoading` `true` suivant `GetForecastAsync` s’établit à tout en obtient des données à partir du serveur. Alors `isLoading` `true`qu’il est, le bouton est désactivé dans l’interface utilisateur:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Le modèle de garde démontré dans l’exemple précédent fonctionne si l’opération de fond est exécutée asynchronement avec le `async` - `await` modèle.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Annuler tôt et éviter l’utilisation après-disposer

En plus d’utiliser un garde tel que décrit dans la [section De la Garde contre plusieurs dépêches,](#guard-against-multiple-dispatches) envisagez d’utiliser un <xref:System.Threading.CancellationToken> pour annuler les opérations de longue durée lorsque le composant est éliminé. Cette approche a l’avantage supplémentaire d’éviter *l’utilisation après l’élimination* des composants :

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Évitez les événements qui produisent de grandes quantités de données

Certains événements DOM, `oninput` `onscroll`tels que ou , peuvent produire une grande quantité de données. Évitez d’utiliser Blazor ces événements dans les applications serveur.

## <a name="additional-security-guidance"></a>Orientations de sécurité supplémentaires

Les conseils pour sécuriser ASP.NET applications Blazor Core s’appliquent aux applications Server et sont couverts dans les sections suivantes :

* [Enregistrement et données sensibles](#logging-and-sensitive-data)
* [Protéger les informations en transit avec HTTPS](#protect-information-in-transit-with-https)
* [Script cross-site (XSS)](#cross-site-scripting-xss))
* [Protection inter-origine](#cross-origin-protection)
* [Cliquez-jacking](#click-jacking)
* [Ouvrez les redirections](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Enregistrement et données sensibles

Les interactions interop JS entre le client et le <xref:Microsoft.Extensions.Logging.ILogger> serveur sont enregistrées dans les journaux du serveur avec des instances. Blazorévite l’enregistrement d’informations sensibles, telles que des événements réels ou des entrées et sorties interop JS.

Lorsqu’une erreur se produit sur le serveur, le cadre informe le client et déchire la session. Par défaut, le client reçoit un message d’erreur générique qui peut être vu dans les outils de développeur du navigateur.

L’erreur côté client n’inclut pas le site d’appels et ne fournit pas de détails sur la cause de l’erreur, mais les journaux du serveur contiennent de telles informations. À des fins de développement, des informations sensibles sur les erreurs peuvent être mises à la disposition du client en permettant des erreurs détaillées.

Activez des erreurs détaillées avec :

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`clé de configuration. Par exemple, `ASPNETCORE_DETAILEDERRORS` définissez la variable `true`de l’environnement à une valeur de .

> [!WARNING]
> L’exposition d’informations d’erreur aux clients sur Internet est un risque pour la sécurité qui doit toujours être évité.

### <a name="protect-information-in-transit-with-https"></a>Protéger les informations en transit avec HTTPS

BlazorUtilisations SignalR du serveur pour la communication entre le client et le serveur. BlazorServer utilise normalement le SignalR transport qui négocie, qui est généralement WebSockets.

BlazorServer n’assure pas l’intégrité et la confidentialité des données envoyées entre le serveur et le client. Utilisez toujours HTTPS.

### <a name="cross-site-scripting-xss"></a>Script cross-site (XSS)

Le script inter-sites (XSS) permet à une partie non autorisée d’exécuter une logique arbitraire dans le contexte du navigateur. Une application compromise pourrait potentiellement exécuter du code arbitraire sur le client. La vulnérabilité pourrait être utilisée pour effectuer potentiellement un certain nombre d’actions malveillantes contre le serveur:

* Envoyer des événements faux/invalides au serveur.
* Répartition échouer / invalider rendre les achèvements.
* Évitez d’envoyer des achèvements de rendu.
* Envoi d’appels interop de JavaScript à .NET.
* Modifier la réponse des appels interop de .NET à JavaScript.
* Évitez d’envoyer .NET aux résultats de JS interop.

Le Blazor cadre Server prend des mesures pour se protéger contre certaines des menaces précédentes :

* Cesse de produire de nouvelles mises à jour de l’interface utilisateur si le client ne reconnaît pas les lots de rendu. Configuré `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`avec .
* Temps n’importe quel .NET à JavaScript appel après une minute sans recevoir une réponse du client. Configuré `CircuitOptions.JSInteropDefaultCallTimeout`avec .
* Effectue la validation de base sur toutes les entrées provenant du navigateur pendant JS interop :
  * .Les références .NET sont valides et du type attendu par la méthode .NET.
  * Les données ne sont pas mal formées.
  * Le nombre correct d’arguments pour la méthode sont présents dans la charge utile.
  * Les arguments ou le résultat peuvent être déséialisés correctement avant d’invoquer la méthode.
* Effectue la validation de base dans toutes les entrées provenant du navigateur à partir d’événements expédiés :
  * L’événement a un type valide.
  * Les données de l’événement peuvent être déséialisées.
  * Il y a un gestionnaire d’événements associé à l’événement.

En plus des garanties que le cadre met en œuvre, l’application doit être codée par le développeur pour se prémunir contre les menaces et prendre les mesures appropriées :

* Validez toujours les données lors du traitement des événements.
* Prenez les mesures appropriées dès la réception des données non valides :
  * Ignorer les données et le retour. Cela permet à l’application de continuer à traiter les demandes.
  * Si l’application détermine que l’entrée est illégitime et ne pourrait pas être produite par un client légitime, jetez une exception. Lancer une exception déchire le circuit et termine la séance.
* Ne faites pas confiance au message d’erreur fourni par les achèvements de lots de rendu inclus dans les journaux. L’erreur est fournie par le client et ne peut généralement pas faire confiance, car le client peut être compromis.
* Ne faites pas confiance à l’entrée sur les appels interop JS dans les deux sens entre les méthodes JavaScript et .NET.
* L’application est chargée de valider que le contenu des arguments et des résultats est valide, même si les arguments ou les résultats sont correctement déséialisés.

Pour qu’une vulnérabilité XSS existe, l’application doit intégrer l’entrée de l’utilisateur dans la page rendue. BlazorLes composants du serveur exécutent une étape de compilation-temps où la majoration dans un fichier *.razor* est transformée en logique procédurale C. Au moment de l’exécution, la logique Cmd construit un *arbre de rendu* décrivant les éléments, le texte et les composants de l’enfant. Ceci est appliqué au DOM du navigateur via une séquence d’instructions JavaScript (ou est sérialisé en HTML dans le cas de prerendering):

* L’entrée de l’utilisateur rendue via `@someStringValue`la syntaxe Razor normale (par exemple, ) n’expose pas une vulnérabilité XSS parce que la syntaxe Razor est ajoutée au DOM via des commandes qui ne peuvent écrire que du texte. Même si la valeur inclut la balisage HTML, la valeur est affichée sous forme de texte statique. Lors du préréaidage, la sortie est codée HTML, qui affiche également le contenu sous forme de texte statique.
* Les balises de script ne sont pas autorisées et ne devraient pas être incluses dans l’arbre de rendu des composants de l’application. Si une balise de script est incluse dans le balisage d’un composant, une erreur de compilation est générée.
* Les auteurs de composants peuvent écrire des composants dans Cmd sans utiliser Razor. L’auteur du composant est responsable de l’utilisation des API correctes lors de l’émission de sortie. Par exemple, `builder.AddContent(0, someUserSuppliedString)` utiliser et *non,* `builder.AddMarkupContent(0, someUserSuppliedString)`car ce dernier pourrait créer une vulnérabilité XSS.

Dans le cadre de la protection contre les attaques XSS, envisager la mise en œuvre des atténuations XSS, telles que [la politique de sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Pour plus d’informations, consultez <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Protection inter-origine

Les attaques d’origine croisée impliquent un client d’origine différente effectuant une action contre le serveur. L’action malveillante est généralement une demande GET ou un formulaire POST (Cross-Site Request Forgery, CSRF), mais l’ouverture d’un WebSocket malveillant est également possible. BlazorLes applications serveur offrent [les SignalR mêmes garanties que toute autre application utilisant l’offre de protocole hub](xref:signalr/security):

* BlazorLes applications serveur peuvent être consultées en sous-origine à moins que des mesures supplémentaires ne soient prises pour l’empêcher. Pour désactiver l’accès à l’origine croisée, soit désactiver corS dans le point `DisableCorsAttribute` d’arrivée en ajoutant le middleware CORS au pipeline et en ajoutant les métadonnées au Blazor point de terminaison ou de limiter l’ensemble des origines autorisées en configurant [ SignalR pour le partage des ressources d’origine croisée](xref:signalr/security#cross-origin-resource-sharing).
* Si corS est activé, des étapes supplémentaires pourraient être nécessaires pour protéger l’application en fonction de la configuration CORS. Si CORS est activé à l’échelle Blazor mondiale, CORS peut être désactivé pour le hub Server en ajoutant les `DisableCorsAttribute` métadonnées aux métadonnées de point de terminaison après avoir appelé `hub.MapBlazorHub()`.

Pour plus d’informations, consultez <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Cliquez-jacking

Click-jacking consiste à rendre `<iframe>` un site à l’intérieur d’un site d’origine différente afin de tromper l’utilisateur en exécutant des actions sur le site attaqué.

Pour protéger une application contre `<iframe>`le rendu à l’intérieur d’une politique de [sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) et l’en-tête. `X-Frame-Options` Pour plus d’informations, consultez [LES documents Web MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Ouvrez les redirections

Lorsque Blazor une session d’application Server commence, le serveur effectue la validation de base des URL envoyées dans le cadre du début de la session. Le cadre vérifie que l’URL de base est un parent de l’URL actuelle avant d’établir le circuit. Aucun contrôle supplémentaire n’est effectué par le cadre.

Lorsqu’un utilisateur sélectionne un lien sur le client, l’URL du lien est envoyée au serveur, ce qui détermine les mesures à prendre. Par exemple, l’application peut effectuer une navigation côté client ou indiquer au navigateur d’aller au nouvel emplacement.

Les composants peuvent également déclencher des demandes `NavigationManager`de navigation programatiquement grâce à l’utilisation de . Dans de tels scénarios, l’application peut effectuer une navigation côté client ou indiquer au navigateur d’aller au nouvel emplacement.

Les composants doivent :

* Évitez d’utiliser l’entrée de l’utilisateur dans le cadre des arguments d’appel de navigation.
* Validez les arguments pour s’assurer que la cible est autorisée par l’application.

Dans le cas contraire, un utilisateur malveillant peut forcer le navigateur à se rendre sur un site contrôlé par l’attaquant. Dans ce scénario, l’attaquant incite l’application à utiliser une partie `NavigationManager.Navigate` de l’entrée de l’utilisateur dans le cadre de l’invocation de la méthode.

Ce conseil s’applique également lors du rendu des liens dans le cadre de l’application :

* Si possible, utilisez des liens relatifs.
* Validez que les destinations de lien absolu sont valables avant de les inclure dans une page.

Pour plus d’informations, consultez <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Pour vous en indication sur <xref:security/blazor/index>l’authentification et l’autorisation, voir .

## <a name="security-checklist"></a>Liste de contrôle de sécurité

La liste suivante des considérations de sécurité n’est pas exhaustive :

* Validez les arguments des événements.
* Validez les entrées et les résultats des appels interop JS.
* Évitez d’utiliser (ou de valider à l’avance) l’entrée de l’utilisateur pour les appels interop .NET à JS.
* Empêcher le client d’allouer une quantité de mémoire non liée.
  * Données dans le composant.
  * `DotNetObject`références retournées au client.
* Garde contre plusieurs dépêches.
* Annulez les opérations de longue durée lorsque le composant est éliminé.
* Évitez les événements qui produisent de grandes quantités de données.
* Évitez d’utiliser l’entrée `NavigationManager.Navigate` de l’utilisateur dans le cadre d’appels et de valider l’entrée de l’utilisateur pour les URL par rapport à un ensemble d’origines autorisées d’abord si inévitable.
* Ne prenez pas de décisions d’autorisation en fonction de l’état de l’interface utilisateur, mais seulement de l’état de la composante.
* Envisagez d’utiliser [la politique de sécurité du contenu (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) pour vous protéger contre les attaques XSS.
* Envisagez d’utiliser CSP et [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) pour vous protéger contre le clic-jacking.
* Assurez-vous que les paramètres CORS sont appropriés Blazor lorsque vous activez CORS ou désactivez explicitement le CORS pour les applications.
* Testez pour vous assurer que Blazor les limites côté serveur pour l’application offrent une expérience utilisateur acceptable sans niveaux de risque inacceptables.
