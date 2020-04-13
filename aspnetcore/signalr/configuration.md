---
title: configuration ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment configurer ASP.NET SignalR applications Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223983"
---
# <a name="aspnet-core-signalr-configuration"></a>Configuration d’ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Options de sérialisation JSON/MessagePack

ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html). Chaque protocole a des options de configuration de sérialisation.

La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode [d’extension AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) `AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`. La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet. La [propriété PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> cet objet est un objet qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour. Pour plus d’informations, voir la [documentation System.Text.Json](/dotnet/api/system.text.json).

Par exemple, pour configurer le sérialisateur pour ne pas modifier le boîtier des noms de `Startup.ConfigureServices`propriété, au lieu des noms par défaut "camelCase", utilisez le code suivant dans :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.

### <a name="switch-to-newtonsoftjson"></a>Passez à Newtonsoft.Json

Si vous avez `Newtonsoft.Json` besoin de fonctionnalités de ce qui ne sont pas pris en charge , `System.Text.Json`Voir Passer à [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Options de sérialisation MessagePack

La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.

## <a name="configure-server-options"></a>Configurer les options de serveur

Le tableau suivant décrit les options de configuration des moyeux SignalR :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondes | Le serveur considérera le client déconnecté s’il n’a pas reçu de message (y compris le maintien en vie) dans cet intervalle. Il pourrait prendre plus de temps que cet intervalle de délai d’attente pour le client d’être effectivement marqué déconnecté, en raison de la façon dont cela est mis en œuvre. La valeur recommandée `KeepAliveInterval` est le double de la valeur.|
| `HandshakeTimeout` | 15 secondes | Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée. Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte. Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client. La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.  |
| `SupportedProtocols` | Tous les protocoles installés | Protocoles soutenus par ce hub. Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels. |
| `EnableDetailedErrors` | `false` | Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub. La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles. |
| `StreamBufferCapacity` | `10` | Le nombre maximum d’éléments qui peuvent être tamponnés pour les flux de téléchargement des clients. Si cette limite est atteinte, le traitement des invocations est bloqué jusqu’à ce que le serveur traite les éléments du flux.|
| `MaximumReceiveMessageSize` | 32 Ko | Taille maximale d’un seul message de hub entrant. |

Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Options avancées de configuration HTTP

Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire. Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 Ko | Le nombre maximum d’octets reçus du client que le serveur tampons avant d’appliquer backpressure. L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands plus rapidement sans appliquer la rétropression, mais peut augmenter la consommation de mémoire. |
| `AuthorizationData` | Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub. | Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub. |
| `TransportMaxBufferSize` | 32 Ko | Le nombre maximum d’octets envoyés par l’application que le serveur tampons avant d’observer la rétropression. L’augmentation de cette valeur permet au serveur de tamponner les messages plus grands plus rapidement sans attendre backpressure, mais peut augmenter la consommation de mémoire. |
| `Transports` | Tous les transports sont activés. | Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter. |
| `LongPolling` | Voir ci-dessous. | Options supplémentaires spécifiques au transport à long scrutin. |
| `WebSockets` | Voir ci-dessous. | Options supplémentaires spécifiques au transport WebSockets. |

Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondes | Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage. La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage. |

Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondes | Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue. |
| `SubProtocolSelector` | `null` | Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée. Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée. |

## <a name="configure-client-options"></a>Configurer les options client

Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript). Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.

### <a name="configure-logging"></a>Configuration de la journalisation

L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode. Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur. Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.

> [!NOTE]
> Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires. Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.

Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet. Appelez `AddConsole` la méthode d’extension :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Dans le client JavaScript, une méthode similaire `configureLogging` existe. Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire. Les journaux sont écrits sur la fenêtre de la console du navigateur.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Au lieu `LogLevel` d’une valeur, `string` vous pouvez également fournir une valeur représentant un nom de niveau de journal. Ceci est utile lors de la configuration de l’enregistrement SignalR `LogLevel` dans des environnements où vous n’avez pas accès aux constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Le tableau suivant répertorie les niveaux de journal disponibles. La valeur que `configureLogging` vous fournissez pour établir le niveau **minimum** de journal qui sera enregistré. Les messages enregistrés à ce niveau, **ou les niveaux énumérés après lui dans le tableau,** seront enregistrés.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**ou**`information` | `LogLevel.Information` |
| `warn`**ou**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.

Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).

Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion. Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière. L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Cela peut être ignoré en toute sécurité.

### <a name="configure-allowed-transports"></a>Configurer les transports autorisés

Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript). Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés. Tous les transports sont activés par défaut.

Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Dans cette version des websockets clients Java est le seul transport disponible.

Dans le client Java, le `withTransport` transport est `HttpHubConnectionBuilder`sélectionné avec la méthode sur le . Le client Java par défaut à l’utilisation du transport WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Le client SignalR Java ne prend pas encore en charge le repli des transports.

### <a name="configure-bearer-authentication"></a>Configurer l’authentification du porteur

Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité. Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ). Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web). Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .

Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurer le délai d’attente et les options de maintien en vie

D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:

# <a name="net"></a>[.NET](#tab/dotnet)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript). Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |
| `HandshakeTimeout` | 15 secondes | Délai pour la poignée de main initiale du serveur. Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript). Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Détermine l’intervalle auquel le client envoie des messages ping. Envoi de tout message du client réinitialise la minuterie au début de l’intervalle. Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté. |

Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement. Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |
| `keepAliveIntervalInMilliseconds` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. Envoi de tout message du client réinitialise la minuterie au début de l’intervalle. Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté. |

# <a name="java"></a>[Java](#tab/java)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement. Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |
| `withHandshakeResponseTimeout` | 15 secondes | Délai pour la poignée de main initiale du serveur. Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement. Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, consultez la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. Envoi de tout message du client réinitialise la minuterie au début de l’intervalle. Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté. |

---

### <a name="configure-additional-options"></a>Configurer des options supplémentaires

D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :

# <a name="net"></a>[.NET](#tab/dotnet)

| Option .NET |  Valeur par défaut | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `SkipNegotiation` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |
| `ClientCertificates` | Vide | Une collection de certificats TLS à envoyer pour authentifier les demandes. |
| `Cookies` | Vide | Une collection de cookies HTTP à envoyer à chaque demande HTTP. |
| `Credentials` | Vide | Informations d’identification à envoyer à chaque demande HTTP. |
| `CloseTimeout` | 5 secondes | WebSockets seulement. Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche. Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte. |
| `Headers` | Vide | Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP. |
| `HttpMessageHandlerFactory` | `null` | Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP. Non utilisé pour les connexions WebSocket. Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre. Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance. **Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.** |
| `Proxy` | `null` | Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP. |
| `UseDefaultCredentials` | `false` | Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets. Cela permet l’utilisation de l’authentification Windows. |
| `WebSocketConfiguration` | `null` | Un délégué qui peut être utilisé pour configurer d’autres options WebSocket. Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript Option | Valeur par défaut | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `skipNegotiation` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |

# <a name="java"></a>[Java](#tab/java)

| Java Option | Valeur par défaut | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `shouldSkipNegotiate` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |
| `withHeader` `withHeaders` | Vide | Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP. |

---

Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Options de sérialisation JSON/MessagePack

ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html). Chaque protocole a des options de configuration de sérialisation.

La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode `Startup.ConfigureServices` [d’extension AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode. La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet. La [propriété PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet `JsonSerializerSettings` est un objet JSON.NET qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour. Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
À titre d’exemple, pour configurer le sérialisateur pour utiliser les noms de propriété "PascalCase", au lieu des noms par défaut "camelCase", utilisez le code suivant dans `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.

### <a name="messagepack-serialization-options"></a>Options de sérialisation MessagePack

La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.

## <a name="configure-server-options"></a>Configurer les options de serveur

Le tableau suivant décrit les options de configuration des moyeux SignalR :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondes | Le serveur considérera le client déconnecté s’il n’a pas reçu de message (y compris le maintien en vie) dans cet intervalle. Il pourrait prendre plus de temps que cet intervalle de délai d’attente pour le client d’être effectivement marqué déconnecté, en raison de la façon dont cela est mis en œuvre. La valeur recommandée `KeepAliveInterval` est le double de la valeur.|
| `HandshakeTimeout` | 15 secondes | Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée. Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte. Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client. La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.  |
| `SupportedProtocols` | Tous les protocoles installés | Protocoles soutenus par ce hub. Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels. |
| `EnableDetailedErrors` | `false` | Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub. La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles. |

Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Options avancées de configuration HTTP

Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire. Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 Ko | Le nombre maximum d’octets reçus du client que le serveur tampons. L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire. |
| `AuthorizationData` | Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub. | Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub. |
| `TransportMaxBufferSize` | 32 Ko | Le nombre maximum d’octets envoyés par l’application que le serveur tampons. L’augmentation de cette valeur permet au serveur d’envoyer des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire. |
| `Transports` | Tous les transports sont activés. | Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter. |
| `LongPolling` | Voir ci-dessous. | Options supplémentaires spécifiques au transport à long scrutin. |
| `WebSockets` | Voir ci-dessous. | Options supplémentaires spécifiques au transport WebSockets. |

Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondes | Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage. La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage. |

Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondes | Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue. |
| `SubProtocolSelector` | `null` | Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée. Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée. |

## <a name="configure-client-options"></a>Configurer les options client

Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript). Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.

### <a name="configure-logging"></a>Configuration de la journalisation

L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode. Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur. Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.

> [!NOTE]
> Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires. Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.

Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet. Appelez `AddConsole` la méthode d’extension :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Dans le client JavaScript, une méthode similaire `configureLogging` existe. Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire. Les journaux sont écrits sur la fenêtre de la console du navigateur.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.

Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).

Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion. Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière. L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Cela peut être ignoré en toute sécurité.

### <a name="configure-allowed-transports"></a>Configurer les transports autorisés

Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript). Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés. Tous les transports sont activés par défaut.

Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Dans cette version des websockets clients Java est le seul transport disponible.

### <a name="configure-bearer-authentication"></a>Configurer l’authentification du porteur

Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité. Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ). Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web). Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .

Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurer le délai d’attente et les options de maintien en vie

D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:

# <a name="net"></a>[.NET](#tab/dotnet)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript). Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |
| `HandshakeTimeout` | 15 secondes | Délai pour la poignée de main initiale du serveur. Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript). Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Détermine l’intervalle auquel le client envoie des messages ping. Envoi de tout message du client réinitialise la minuterie au début de l’intervalle. Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté. |

Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement. Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |
| `keepAliveIntervalInMilliseconds` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. Envoi de tout message du client réinitialise la minuterie au début de l’intervalle. Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté. |

# <a name="java"></a>[Java](#tab/java)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement. Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |
| `withHandshakeResponseTimeout` | 15 secondes | Délai pour la poignée de main initiale du serveur. Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement. Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, consultez la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondes (15 000 millisecondes) | Détermine l’intervalle auquel le client envoie des messages ping. Envoi de tout message du client réinitialise la minuterie au début de l’intervalle. Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté. |

---

### <a name="configure-additional-options"></a>Configurer des options supplémentaires

D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :

# <a name="net"></a>[.NET](#tab/dotnet)

| Option .NET |  Valeur par défaut | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `SkipNegotiation` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |
| `ClientCertificates` | Vide | Une collection de certificats TLS à envoyer pour authentifier les demandes. |
| `Cookies` | Vide | Une collection de cookies HTTP à envoyer à chaque demande HTTP. |
| `Credentials` | Vide | Informations d’identification à envoyer à chaque demande HTTP. |
| `CloseTimeout` | 5 secondes | WebSockets seulement. Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche. Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte. |
| `Headers` | Vide | Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP. |
| `HttpMessageHandlerFactory` | `null` | Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP. Non utilisé pour les connexions WebSocket. Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre. Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance. **Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.** |
| `Proxy` | `null` | Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP. |
| `UseDefaultCredentials` | `false` | Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets. Cela permet l’utilisation de l’authentification Windows. |
| `WebSocketConfiguration` | `null` | Un délégué qui peut être utilisé pour configurer d’autres options WebSocket. Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript Option | Valeur par défaut | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `skipNegotiation` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |

# <a name="java"></a>[Java](#tab/java)

| Java Option | Valeur par défaut | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `shouldSkipNegotiate` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |
| `withHeader` `withHeaders` | Vide | Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP. |

---

Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Options de sérialisation JSON/MessagePack

ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html). Chaque protocole a des options de configuration de sérialisation.

La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode `Startup.ConfigureServices` [d’extension AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode. La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet. La [propriété PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet `JsonSerializerSettings` est un objet JSON.NET qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour. Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
À titre d’exemple, pour configurer le sérialisateur pour utiliser les noms de propriété "PascalCase", au lieu des noms par défaut "camelCase", utilisez le code suivant dans `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.

### <a name="messagepack-serialization-options"></a>Options de sérialisation MessagePack

La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.

> [!NOTE]
> Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.

## <a name="configure-server-options"></a>Configurer les options de serveur

Le tableau suivant décrit les options de configuration des moyeux SignalR :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 secondes | Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée. Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 secondes | Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte. Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client. La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.  |
| `SupportedProtocols` | Tous les protocoles installés | Protocoles soutenus par ce hub. Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels. |
| `EnableDetailedErrors` | `false` | Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub. La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles. |

Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Options avancées de configuration HTTP

Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire. Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 Ko | Le nombre maximum d’octets reçus du client que le serveur tampons. L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire. |
| `AuthorizationData` | Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub. | Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub. |
| `TransportMaxBufferSize` | 32 Ko | Le nombre maximum d’octets envoyés par l’application que le serveur tampons. L’augmentation de cette valeur permet au serveur d’envoyer des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire. |
| `Transports` | Tous les transports sont activés. | Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter. |
| `LongPolling` | Voir ci-dessous. | Options supplémentaires spécifiques au transport à long scrutin. |
| `WebSockets` | Voir ci-dessous. | Options supplémentaires spécifiques au transport WebSockets. |

Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondes | Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage. La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage. |

Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondes | Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue. |
| `SubProtocolSelector` | `null` | Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée. Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée. |

## <a name="configure-client-options"></a>Configurer les options client

Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript). Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.

### <a name="configure-logging"></a>Configuration de la journalisation

L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode. Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur. Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.

> [!NOTE]
> Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires. Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.

Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet. Appelez `AddConsole` la méthode d’extension :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Dans le client JavaScript, une méthode similaire `configureLogging` existe. Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire. Les journaux sont écrits sur la fenêtre de la console du navigateur.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.

Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).

Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion. Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière. L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Cela peut être ignoré en toute sécurité.

### <a name="configure-allowed-transports"></a>Configurer les transports autorisés

Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript). Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés. Tous les transports sont activés par défaut.

Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurer l’authentification du porteur

Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité. Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ). Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web). Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .

Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurer le délai d’attente et les options de maintien en vie

D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:

# <a name="net"></a>[.NET](#tab/dotnet)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript). Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |
| `HandshakeTimeout` | 15 secondes | Délai pour la poignée de main initiale du serveur. Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript). Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement. Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver. |

# <a name="java"></a>[Java](#tab/java)

| Option | Valeur par défaut | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondes (30 000 millisecondes) | Délai d’attente pour l’activité du serveur. Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement. Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente. La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur, pour laisser le temps pour pings d’arriver. |
| `withHandshakeResponseTimeout` | 15 secondes | Délai pour la poignée de main initiale du serveur. Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement. Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère. Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurer des options supplémentaires

D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :

# <a name="net"></a>[.NET](#tab/dotnet)

| Option .NET |  Valeur par défaut | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `SkipNegotiation` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |
| `ClientCertificates` | Vide | Une collection de certificats TLS à envoyer pour authentifier les demandes. |
| `Cookies` | Vide | Une collection de cookies HTTP à envoyer à chaque demande HTTP. |
| `Credentials` | Vide | Informations d’identification à envoyer à chaque demande HTTP. |
| `CloseTimeout` | 5 secondes | WebSockets seulement. Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche. Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte. |
| `Headers` | Vide | Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP. |
| `HttpMessageHandlerFactory` | `null` | Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP. Non utilisé pour les connexions WebSocket. Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre. Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance. **Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.** |
| `Proxy` | `null` | Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP. |
| `UseDefaultCredentials` | `false` | Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets. Cela permet l’utilisation de l’authentification Windows. |
| `WebSocketConfiguration` | `null` | Un délégué qui peut être utilisé pour configurer d’autres options WebSocket. Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript Option | Valeur par défaut | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `skipNegotiation` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |

# <a name="java"></a>[Java](#tab/java)

| Java Option | Valeur par défaut | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP. |
| `shouldSkipNegotiate` | `false` | Réglez `true` ceci pour sauter l’étape de négociation. **Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**. Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure. |
| `withHeader` `withHeaders` | Vide | Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP. |

---

Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
