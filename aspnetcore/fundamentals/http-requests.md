---
title: Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core
author: stevejgordon
description: Découvrez plus d’informations sur l’utilisation de l’interface IHttpClientFactory pour gérer des instances HttpClient logiques dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661685"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), Rick [Anderson](https://twitter.com/RickAndMSFT), et [Kirk Larkin](https://github.com/serpent5)

Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application. `IHttpClientFactory`offre les avantages suivants :

* Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques. Par exemple, un client nommé *github* peut être enregistré et configuré pour accéder à [GitHub](https://github.com/). Un client par défaut peut être enregistré pour un accès général.
* Codifie le concept de middleware sortant via `HttpClient`déléguer les gestionnaires dans . Fournit des extensions pour Polly-basé middleware pour profiter `HttpClient`de la délégation des gestionnaires dans .
* Gère la mise en `HttpClientMessageHandler` commun et la durée de vie des instances sous-jacentes. La gestion automatique évite les problèmes communs de DNS `HttpClient` (Domain Name System) qui se produisent lors de la gestion manuelle des durées de vie.
* Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.

[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).

Le code de l’échantillon dans cette version thématique utilise <xref:System.Text.Json> pour déséialiser le contenu JSON retourné dans les réponses HTTP. Pour les `Json.NET` échantillons `ReadAsAsync<T>`qui utilisent et, utilisez le sélecteur de la version pour sélectionner une version 2.x de ce sujet.

## <a name="consumption-patterns"></a>Modèles de consommation

Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :

* [Utilisation de base](#basic-usage)
* [Clients nommés](#named-clients)
* [Clients typés](#typed-clients)
* [Clients générés](#generated-clients)

La meilleure approche dépend des exigences de l’application.

### <a name="basic-usage"></a>Utilisation de base

`IHttpClientFactory`peut être enregistré `AddHttpClient`en appelant :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Une `IHttpClientFactory` boîte peut être demandée en utilisant [l’injection de dépendance (DI)](xref:fundamentals/dependency-injection). Le code `IHttpClientFactory` suivant sert `HttpClient` à créer une instance :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

L’utilisation `IHttpClientFactory` comme dans l’exemple précédent est un bon moyen de refactorer une application existante. Il n’a `HttpClient` aucun impact sur la façon dont est utilisé. Dans les `HttpClient` endroits où les instances sont créées dans <xref:System.Net.Http.IHttpClientFactory.CreateClient*>une application existante, remplacez ces événements par des appels vers .

### <a name="named-clients"></a>Clients nommés

Les clients nommés sont un bon choix lorsque :

* L’application nécessite de `HttpClient`nombreuses utilisations distinctes de .
* Beaucoup `HttpClient`ont une configuration différente.

Configuration d’un nommé `HttpClient` peut `Startup.ConfigureServices`être spécifié lors de l’enregistrement dans :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Dans le code précédent, le client est configuré avec :

* L’adresse `https://api.github.com/`de base .
* Deux en-têtes nécessaires pour travailler avec l’API GitHub.

#### <a name="createclient"></a>CréerClient

Chaque <xref:System.Net.Http.IHttpClientFactory.CreateClient*> fois est appelé:

* Une nouvelle `HttpClient` instance de est créée.
* L’action de configuration est appelée.

Pour créer un client nommé, `CreateClient`transmettez son nom à :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte. Le code peut passer juste le chemin, puisque l’adresse de base configurée pour le client est utilisée.

### <a name="typed-clients"></a>Clients typés

Clients typés :

* Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.
* Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.
* Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier. Par exemple, un seul client dactylographe peut être utilisé :
  * Pour un seul backend endpoint.
  * Pour encapsuler toute logique traitant du point de terminaison.
* Travaillez avec DI et peut être injecté si nécessaire dans l’application.

Un client dactylographié accepte un `HttpClient` paramètre dans son constructeur :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Dans le code précédent :

* La configuration est déplacée dans le client dactylographe.
* L’objet `HttpClient` est exposé en tant que propriété publique.

Des méthodes spécifiques à l’API peuvent être créées qui exposent les `HttpClient` fonctionnalités. Par exemple, `GetAspNetDocsIssues` la méthode encapsule le code pour récupérer les problèmes ouverts.

Le code <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> suivant `Startup.ConfigureServices` appelle pour enregistrer une classe de clients dactylographes :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Le client typé est inscrit comme étant transitoire avec injection de dépendances. Dans le code `AddHttpClient` précédent, s’inscrit `GitHubService` comme un service transitoire. Cette inscription utilise une méthode d’usine pour :

1. Créez une instance de `HttpClient`.
1. Créer un `GitHubService`exemple de , `HttpClient` en passant dans le cas de son constructeur.

Le client typé peut être injecté et utilisé directement :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

La configuration d’un client dactylographié peut être spécifiée lors de l’inscription dans, `Startup.ConfigureServices`plutôt que dans le constructeur du client dactylographié :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Le `HttpClient` peut être encapsulé au sein d’un client dactylographe. Plutôt que de l’exposer comme une propriété, définissez une méthode qui appelle l’instance à l’interne `HttpClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Dans le code `HttpClient` précédent, le est stocké dans un champ privé. L’accès `HttpClient` à la `GetRepos` est par la méthode publique.

### <a name="generated-clients"></a>Clients générés

`IHttpClientFactory`peut être utilisé en combinaison avec des bibliothèques tierces telles que [Refit](https://github.com/paulcbetts/refit). Refit est une bibliothèque REST pour .NET. Il convertit les API REST en interfaces dynamiques. Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.

Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Middleware pour les requêtes sortantes

`HttpClient`a le concept de déléguer des gestionnaires qui peuvent être liés ensemble pour les demandes sortantes HTTP. `IHttpClientFactory`:

* Simplifie la définition des gestionnaires à appliquer pour chaque client nommé.
* Soutient l’enregistrement et l’enchaînement de plusieurs gestionnaires pour construire un pipeline middleware de demande sortante. Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante. Ce modèle:

  * Est similaire au pipeline intermédiaire middleware dans ASP.NET Core.
  * Fournit un mécanisme pour gérer les préoccupations transversales autour des demandes de HTTP, telles que :

    * caching
    * gestion des erreurs
    * sérialisation
    * journalisation

Pour créer un gestionnaire délégué :

* Dériver <xref:System.Net.Http.DelegatingHandler>de .
* Substituez <xref:System.Net.Http.DelegatingHandler.SendAsync*> Exécuter le code avant de transmettre la demande au prochain gestionnaire dans le pipeline :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Le code précédent `X-API-KEY` vérifie si l’en-tête est dans la demande. S’il `X-API-KEY` <xref:System.Net.HttpStatusCode.BadRequest> manque, est retourné.

Plus d’un gestionnaire peut être `HttpClient` ajouté <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>à la configuration pour un avec :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances. `IHttpClientFactory` crée une étendue DI distincte pour chaque gestionnaire. Les manutentionnaires peuvent compter sur des services de n’importe quelle portée. Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.

Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.

Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés. Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :

* Passez des données dans le gestionnaire à l’aide [de HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Utilisez <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> pour accéder à la requête en cours.
* Créez un objet de stockage <xref:System.Threading.AsyncLocal`1> personnalisé pour passer les données.

## <a name="use-polly-based-handlers"></a>Utiliser les gestionnaires Polly

`IHttpClientFactory`s’intègre à la bibliothèque tierce [Polly](https://github.com/App-vNext/Polly). Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET. Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).

Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`. Les extensions Polly appuient l’ajout de gestionnaires basés à Polly à ses clients. Polly nécessite le paquet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.

### <a name="handle-transient-faults"></a>Gérer les erreurs temporaires

Les défauts se produisent généralement lorsque les appels HTTP externes sont transitoires. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>permet de définir une stratégie pour traiter les erreurs transitoires. Stratégies configurées avec `AddTransientHttpErrorPolicy` poignée les réponses suivantes :

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy`donne accès `PolicyBuilder` à un objet configuré pour gérer les erreurs représentant un défaut transitoire possible :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie. Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.

### <a name="dynamically-select-policies"></a>Sélectionner dynamiquement des stratégies

Des méthodes d’extension sont fournies pour ajouter <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>des gestionnaires basés à Polly, par exemple. La `AddPolicyHandler` surcharge suivante inspecte la demande de décider quelle politique appliquer :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué. Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.

### <a name="add-multiple-polly-handlers"></a>Ajouter plusieurs gestionnaires Polly

Il est courant de nicher les politiques de Polly :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Dans l'exemple précédent :

* Deux gestionnaires sont ajoutés.
* Le premier <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> gestionnaire utilise pour ajouter une politique de retentie. Les requêtes qui ont échoué sont retentées jusqu’à trois fois.
* Le `AddTransientHttpErrorPolicy` deuxième appel ajoute une politique de disjoncteur. D’autres demandes externes sont bloquées pendant 30 secondes si 5 tentatives infructueuses se produisent séquentiellement. Les stratégies de disjoncteur sont avec état. Tous les appels effectués via ce client partagent le même état du circuit.

### <a name="add-policies-from-the-polly-registry"></a>Ajouter des stratégies à partir du Registre Polly

Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.

Dans le code suivant :

* Les polices «régulières» et «longues» sont ajoutées.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>ajoute les politiques « régulières » et « longues » du registre.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Pour plus `IHttpClientFactory` d’informations sur et les intégrations Polly, voir le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient et gestion de la durée de vie

Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`. Un <xref:System.Net.Http.HttpMessageHandler> est créé par client nommé. La fabrique gère les durées de vie des instances `HttpMessageHandler`.

`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources. Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.

Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes. La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion. Certains gestionnaires gardent également les connexions ouvertes indéfiniment, ce qui peut empêcher le gestionnaire de réagir aux modifications de DNS (Domain Name System).

La durée de vie par défaut d’un gestionnaire est de deux minutes. La valeur par défaut peut être remplacée par client désigné :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient`les instances peuvent généralement être traitées comme des objets .NET **qui ne** nécessitent pas d’élimination. La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.

Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`. Ce modèle devient inutile après la migration vers `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatives à IHttpClientFactory

L’utilisation `IHttpClientFactory` d’une application activée par DI évite :

* Problèmes d’épuisement des `HttpMessageHandler` ressources en mettant en commun les instances.
* Problèmes de DNS `HttpMessageHandler` périmés par des cas de cyclisme à intervalles réguliers.

Il existe d’autres moyens de résoudre <xref:System.Net.Http.SocketsHttpHandler> les problèmes précédents en utilisant une instance à longue durée de vie.

- Créez une `SocketsHttpHandler` instance de la date à laquelle l’application démarre et utilisez-la pour la durée de vie de l’application.
- Configurer <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> à une valeur appropriée basée sur les temps de rafraîchissement DNS.
- Créez `HttpClient` des `new HttpClient(handler, disposeHandler: false)` instances en utilisant au besoin.

Les approches précédentes résolvent `IHttpClientFactory` les problèmes de gestion des ressources qui résolvent de la même manière.

- Les `SocketsHttpHandler` partages `HttpClient` les connexions entre les instances. Ce partage empêche l’épuisement des douilles.
- Les `SocketsHttpHandler` cycles de `PooledConnectionLifetime` connexions selon pour éviter les problèmes de DNS périmés.

### <a name="cookies"></a>Cookies

Les instances `HttpMessageHandler` mises `CookieContainer` en commun se traduit par le partage d’objets. Le partage `CookieContainer` imprévu d’objets entraîne souvent un code incorrect. Pour les applications qui nécessitent des cookies, considérez soit :

 - Désactiver la manipulation automatique des cookies
 - Éviter`IHttpClientFactory`

Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la manipulation automatique des cookies :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Journalisation

Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes. Activez le niveau d’information approprié dans la configuration de connexion pour voir les messages journal par défaut. Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.

La catégorie de journal utilisée pour chaque client comprend le nom du client. Un client nommé *MyNamedClient*, par exemple, enregistre des messages avec une catégorie de "System.Net.Http.HttpClient. **MyNamedClient**. LogicalHandler". Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes. Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée. Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.

La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes. Dans l’exemple *MyNamedClient,* ces messages sont enregistrés avec la catégorie journal "System.Net.Http.HttpClient. **MyNamedClient**. ClientHandler". Pour la demande, cela se produit après que tous les autres gestionnaires se sont enfuis et immédiatement avant l’envoi de la demande. Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.

L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline. Cela peut inclure des modifications aux en-têtes de demande ou au code d’état de réponse.

L’inclusion du nom du client dans la catégorie journal permet le filtrage de journal pour des clients nommés spécifiques.

## <a name="configure-the-httpmessagehandler"></a>Configurer le HttpMessageHandler

Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.

Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés. La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué. Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Utiliser IHttpClientFactory dans une application console

Dans une application console, ajoutez les références de package suivantes au projet :

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Dans l’exemple suivant :

* <xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).
* `MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`. `HttpClient` est utilisé pour récupérer une page web.
* `Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware de propagation d’en-tête

La propagation de l’en-tête est un ASP.NET Core middleware pour propager les en-têtes HTTP de la demande entrante aux demandes sortantes du client HTTP. Pour utiliser la propagation de l’en-tête :

* Référence du package [Microsoft.AspNetCore.HeaderPropagation.](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)
* Configurer le `HttpClient` middleware et dans `Startup`:

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* Le client comprend les en-têtes configurés sur les demandes sortantes :

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implémenter le modèle Disjoncteur](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [Comment sérialiser et déséialiser JSON en .NET](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)

Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application. Elle offre les avantages suivants :

* Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques. Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/). Un client par défaut peut être inscrit à d’autres fins.
* Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.
* Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.
* Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Modèles de consommation

Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :

* [Utilisation de base](#basic-usage)
* [Clients nommés](#named-clients)
* [Clients typés](#typed-clients)
* [Clients générés](#generated-clients)

Aucune d’entre elles n’est meilleure qu’une autre. La meilleure approche dépend des contraintes de l’application.

### <a name="basic-usage"></a>Utilisation de base

Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection). Le `IHttpClientFactory` peut être utilisé `HttpClient` pour créer une instance:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante. Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé. Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clients nommés

Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**. La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*. Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.

Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.

Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`. Spécifiez le nom du client à créer :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte. Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.

### <a name="typed-clients"></a>Clients typés

Clients typés :

* Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.
* Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.
* Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier. Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.
* Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.

Un client dactylographié accepte un `HttpClient` paramètre dans son constructeur :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Dans le code précédent, la configuration est déplacée dans le client typé. L’objet `HttpClient` est exposé en tant que propriété publique. Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`. La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.

Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Le client typé est inscrit comme étant transitoire avec injection de dépendances. Le client typé peut être injecté et utilisé directement :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé. Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Dans le code précédent, le `HttpClient` est stocké en tant que champ privé. Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.

### <a name="generated-clients"></a>Clients générés

`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit). Refit est une bibliothèque REST pour .NET. Il convertit les API REST en interfaces dynamiques. Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.

Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Middleware pour les requêtes sortantes

`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes. Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé. Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes. Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante. Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core. Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.

Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>. Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Le code précédent définit un gestionnaire de base. Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête. Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.

Lors de l’inscription, un ou plusieurs gestionnaires `HttpClient`peuvent être ajoutés à la configuration pour un . Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances. `IHttpClientFactory` crée une étendue DI distincte pour chaque gestionnaire. Les gestionnaires peuvent librement dépendre des services de n’importe quelle étendue. Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.

Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.

Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés. Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :

* Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.
* Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.
* Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.

## <a name="use-polly-based-handlers"></a>Utiliser les gestionnaires Polly

`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly). Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET. Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).

Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`. Les extensions Polly :

* Prennent en charge l’ajout de gestionnaires Polly à des clients.
* Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/). Ce package n’est pas inclus dans le framework partagé ASP.NET Core.

### <a name="handle-transient-faults"></a>Gérer les erreurs temporaires

Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires. Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires. Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.

L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`. L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie. Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.

### <a name="dynamically-select-policies"></a>Sélectionner dynamiquement des stratégies

Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly. Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges. Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué. Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.

### <a name="add-multiple-polly-handlers"></a>Ajouter plusieurs gestionnaires Polly

Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Dans l’exemple précédent, deux gestionnaires sont ajoutés. Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative. Les requêtes qui ont échoué sont retentées jusqu’à trois fois. Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur. Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent. Les stratégies de disjoncteur sont avec état. Tous les appels effectués via ce client partagent le même état du circuit.

### <a name="add-policies-from-the-polly-registry"></a>Ajouter des stratégies à partir du Registre Polly

Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`. Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`. Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.

Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient et gestion de la durée de vie

Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`. Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé. La fabrique gère les durées de vie des instances `HttpMessageHandler`.

`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources. Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.

Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes. La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion. Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.

La durée de vie par défaut d’un gestionnaire est de deux minutes. La valeur par défaut peut être remplacée pour chaque client nommé. Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

La suppression du client n’est pas nécessaire. La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`. Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.

Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`. Ce modèle devient inutile après la migration vers `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatives à IHttpClientFactory

L’utilisation `IHttpClientFactory` d’une application activée par DI évite :

* Problèmes d’épuisement des `HttpMessageHandler` ressources en mettant en commun les instances.
* Problèmes de DNS `HttpMessageHandler` périmés par des cas de cyclisme à intervalles réguliers.

Il existe d’autres moyens de résoudre <xref:System.Net.Http.SocketsHttpHandler> les problèmes précédents en utilisant une instance à longue durée de vie.

- Créez une `SocketsHttpHandler` instance de la date à laquelle l’application démarre et utilisez-la pour la durée de vie de l’application.
- Configurer <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> à une valeur appropriée basée sur les temps de rafraîchissement DNS.
- Créez `HttpClient` des `new HttpClient(handler, disposeHandler: false)` instances en utilisant au besoin.

Les approches précédentes résolvent `IHttpClientFactory` les problèmes de gestion des ressources qui résolvent de la même manière.

- Les `SocketsHttpHandler` partages `HttpClient` les connexions entre les instances. Ce partage empêche l’épuisement des douilles.
- Les `SocketsHttpHandler` cycles de `PooledConnectionLifetime` connexions selon pour éviter les problèmes de DNS périmés.

### <a name="cookies"></a>Cookies

Les instances `HttpMessageHandler` mises `CookieContainer` en commun se traduit par le partage d’objets. Le partage `CookieContainer` imprévu d’objets entraîne souvent un code incorrect. Pour les applications qui nécessitent des cookies, considérez soit :

 - Désactiver la manipulation automatique des cookies
 - Éviter`IHttpClientFactory`

Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la manipulation automatique des cookies :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Journalisation

Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes. Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut. Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.

La catégorie de journal utilisée pour chaque client comprend le nom du client. Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes. Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée. Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.

La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes. Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau. Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.

L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline. Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.

L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.

## <a name="configure-the-httpmessagehandler"></a>Configurer le HttpMessageHandler

Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.

Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés. La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué. Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Utiliser IHttpClientFactory dans une application console

Dans une application console, ajoutez les références de package suivantes au projet :

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Dans l’exemple suivant :

* <xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).
* `MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`. `HttpClient` est utilisé pour récupérer une page web.
* `Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implémenter le modèle Disjoncteur](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)

Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application. Elle offre les avantages suivants :

* Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques. Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/). Un client par défaut peut être inscrit à d’autres fins.
* Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.
* Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.
* Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

Les projets ciblant .NET Framework nécessitent l’installation du package NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/). Les projets qui ciblent .NET Core et référencent le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluent déjà le package `Microsoft.Extensions.Http`.

## <a name="consumption-patterns"></a>Modèles de consommation

Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :

* [Utilisation de base](#basic-usage)
* [Clients nommés](#named-clients)
* [Clients typés](#typed-clients)
* [Clients générés](#generated-clients)

Aucune d’entre elles n’est meilleure qu’une autre. La meilleure approche dépend des contraintes de l’application.

### <a name="basic-usage"></a>Utilisation de base

Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection). Le `IHttpClientFactory` peut être utilisé `HttpClient` pour créer une instance:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante. Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé. Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clients nommés

Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**. La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*. Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.

Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.

Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`. Spécifiez le nom du client à créer :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte. Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.

### <a name="typed-clients"></a>Clients typés

Clients typés :

* Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.
* Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.
* Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier. Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.
* Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.

Un client dactylographié accepte un `HttpClient` paramètre dans son constructeur :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Dans le code précédent, la configuration est déplacée dans le client typé. L’objet `HttpClient` est exposé en tant que propriété publique. Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`. La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.

Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Le client typé est inscrit comme étant transitoire avec injection de dépendances. Le client typé peut être injecté et utilisé directement :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé. Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Dans le code précédent, le `HttpClient` est stocké en tant que champ privé. Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.

### <a name="generated-clients"></a>Clients générés

`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit). Refit est une bibliothèque REST pour .NET. Il convertit les API REST en interfaces dynamiques. Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.

Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Middleware pour les requêtes sortantes

`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes. Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé. Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes. Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante. Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core. Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.

Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>. Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Le code précédent définit un gestionnaire de base. Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête. Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.

Lors de l’inscription, un ou plusieurs gestionnaires `HttpClient`peuvent être ajoutés à la configuration pour un . Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances. Le gestionnaire **doit** être inscrit dans l’injection de dépendances en tant que service temporaire, sans étendue. Si le gestionnaire est inscrit en tant que service étendu et que des services dont dépend le gestionnaire peuvent être supprimés :

* Les services du gestionnaire peuvent être supprimés avant que le gestionnaire ne soit hors de portée.
* Les services du gestionnaire supprimés entraînent un échec du gestionnaire.

Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type du gestionnaire.

Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés. Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :

* Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.
* Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.
* Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.

## <a name="use-polly-based-handlers"></a>Utiliser les gestionnaires Polly

`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly). Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET. Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).

Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`. Les extensions Polly :

* Prennent en charge l’ajout de gestionnaires Polly à des clients.
* Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/). Ce package n’est pas inclus dans le framework partagé ASP.NET Core.

### <a name="handle-transient-faults"></a>Gérer les erreurs temporaires

Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires. Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires. Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.

L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`. L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie. Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.

### <a name="dynamically-select-policies"></a>Sélectionner dynamiquement des stratégies

Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly. Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges. Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué. Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.

### <a name="add-multiple-polly-handlers"></a>Ajouter plusieurs gestionnaires Polly

Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Dans l’exemple précédent, deux gestionnaires sont ajoutés. Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative. Les requêtes qui ont échoué sont retentées jusqu’à trois fois. Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur. Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent. Les stratégies de disjoncteur sont avec état. Tous les appels effectués via ce client partagent le même état du circuit.

### <a name="add-policies-from-the-polly-registry"></a>Ajouter des stratégies à partir du Registre Polly

Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`. Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`. Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.

Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient et gestion de la durée de vie

Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`. Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé. La fabrique gère les durées de vie des instances `HttpMessageHandler`.

`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources. Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.

Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes. La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion. Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.

La durée de vie par défaut d’un gestionnaire est de deux minutes. La valeur par défaut peut être remplacée pour chaque client nommé. Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

La suppression du client n’est pas nécessaire. La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`. Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.

Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`. Ce modèle devient inutile après la migration vers `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatives à IHttpClientFactory

L’utilisation `IHttpClientFactory` d’une application activée par DI évite :

* Problèmes d’épuisement des `HttpMessageHandler` ressources en mettant en commun les instances.
* Problèmes de DNS `HttpMessageHandler` périmés par des cas de cyclisme à intervalles réguliers.

Il existe d’autres moyens de résoudre <xref:System.Net.Http.SocketsHttpHandler> les problèmes précédents en utilisant une instance à longue durée de vie.

- Créez une `SocketsHttpHandler` instance de la date à laquelle l’application démarre et utilisez-la pour la durée de vie de l’application.
- Configurer <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> à une valeur appropriée basée sur les temps de rafraîchissement DNS.
- Créez `HttpClient` des `new HttpClient(handler, disposeHandler: false)` instances en utilisant au besoin.

Les approches précédentes résolvent `IHttpClientFactory` les problèmes de gestion des ressources qui résolvent de la même manière.

- Les `SocketsHttpHandler` partages `HttpClient` les connexions entre les instances. Ce partage empêche l’épuisement des douilles.
- Les `SocketsHttpHandler` cycles de `PooledConnectionLifetime` connexions selon pour éviter les problèmes de DNS périmés.

### <a name="cookies"></a>Cookies

Les instances `HttpMessageHandler` mises `CookieContainer` en commun se traduit par le partage d’objets. Le partage `CookieContainer` imprévu d’objets entraîne souvent un code incorrect. Pour les applications qui nécessitent des cookies, considérez soit :

 - Désactiver la manipulation automatique des cookies
 - Éviter`IHttpClientFactory`

Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la manipulation automatique des cookies :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Journalisation

Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes. Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut. Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.

La catégorie de journal utilisée pour chaque client comprend le nom du client. Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes. Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée. Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.

La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes. Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau. Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.

L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline. Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.

L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.

## <a name="configure-the-httpmessagehandler"></a>Configurer le HttpMessageHandler

Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.

Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés. La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué. Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Utiliser IHttpClientFactory dans une application console

Dans une application console, ajoutez les références de package suivantes au projet :

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Dans l’exemple suivant :

* <xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).
* `MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`. `HttpClient` est utilisé pour récupérer une page web.
* `Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware de propagation d’en-tête

La propagation de l’en-tête est un middleware soutenu par la communauté pour propager les en-têtes HTTP de la demande entrante aux demandes sortantes de client HTTP. Pour utiliser la propagation de l’en-tête :

* Référence le port soutenu par la communauté du paquet [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation). ASP.NET Core 3.1 et prend en charge plus tard [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).

* Configurer le `HttpClient` middleware et dans `Startup`:

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* Le client comprend les en-têtes configurés sur les demandes sortantes :

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implémenter le modèle Disjoncteur](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
