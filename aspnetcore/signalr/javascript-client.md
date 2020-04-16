---
title: ASP.NET client SignalR JavaScript de base
author: bradygaster
description: Aperçu du client SignalR JavaScript de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440855"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET client SignalR JavaScript de base

Par [Rachel Appel](https://twitter.com/rachelappel)

La ASP.NET la SignalR bibliothèque client Core JavaScript permet aux développeurs d’appeler le code du hub côté serveur.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Installer SignalR le forfait client

La SignalR bibliothèque client JavaScript est livrée sous forme de forfait [npm.](https://www.npmjs.com/) Les sections suivantes décrivent différentes façons d’installer la bibliothèque cliente.

### <a name="install-with-npm"></a>Installer avec npm

Si vous utilisez Visual Studio, exécutez les commandes suivantes à partir de **Package Manager Console** dans le dossier racine. Pour Visual Studio Code, exécutez les commandes suivantes à partir du **terminal intégré**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm installe le contenu de l’emballage dans le dossier *node_modules.\\ * Créez un nouveau dossier nommé *signaleur* sous le dossier *lib\\wwwroot.* Copiez le fichier *signalur.js* sur le dossier *wwwroot-lib-signaler.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm installe le contenu de l’emballage dans le dossier *node_modules.\\ * Créez un nouveau dossier nommé *signaleur* sous le dossier *lib\\wwwroot.* Copiez le fichier *signalur.js* sur le dossier *wwwroot-lib-signaler.*

::: moniker-end

Référence SignalR au client JavaScript dans l’élément. `<script>` Par exemple :

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Utiliser un réseau de diffusion de contenu (CDN)

Pour utiliser la bibliothèque cliente sans la condition préalable de npm, faites référence à une copie hébergée par CDN de la bibliothèque cliente. Par exemple :

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

La bibliothèque cliente est disponible sur les CDN suivants :

::: moniker range=">= aspnetcore-3.0"

* [cdnjs (cdnjs)](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs (cdnjs)](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Installer avec LibMan

[LibMan](xref:client-side/libman/index) peut être utilisé pour installer des fichiers spécifiques de bibliothèque client de la bibliothèque cliente hébergée par CDN. Par exemple, n’ajoutez que le fichier JavaScript minifié au projet. Pour plus de détails sur cette approche, voir [Ajouter la SignalR bibliothèque client](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Connectez-vous à un hub

Le code suivant crée et démarre une connexion. Le nom du hub est insensible au cas.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Connexions d’origine croisée

En règle générale, les navigateurs chargent les connexions à partir du même domaine que la page demandée. Cependant, il y a des occasions où une connexion à un autre domaine est nécessaire.

Pour empêcher un site malveillant de lire des données sensibles à partir d’un autre site, [les connexions inter-origines](xref:security/cors) sont désactivées par défaut. Pour permettre une demande d’origine `Startup` croisée, activez-la dans la classe.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Méthodes de moyeu d’appel du client

Les clients JavaScript appellent des méthodes publiques sur les hubs via la méthode [d’invoquer](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de la [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). La `invoke` méthode accepte deux arguments :

* Le nom de la méthode hub. Dans l’exemple suivant, le nom `SendMessage`de la méthode sur le moyeu est .
* Tous les arguments définis dans la méthode du hub. Dans l’exemple suivant, `message`le nom de l’argument est . L’exemple code utilise la syntaxe fonction fléchée qui est pris en charge dans les versions actuelles de tous les principaux navigateurs, sauf Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Si vous utilisez Azure SignalR Service en mode Sans *serveur,* vous ne pouvez pas appeler les méthodes de moyeu d’un client. Pour plus d’informations, consultez la [ SignalR documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)du Service .

La `invoke` méthode renvoie une [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript . Le `Promise` est résolu avec la valeur de retour (le cas échéant) lorsque la méthode sur le serveur revient. Si la méthode sur le serveur `Promise` lance une erreur, la est rejetée avec le message d’erreur. Utilisez `then` les `catch` méthodes `Promise` et les méthodes en `await` soi pour traiter ces cas (ou syntaxe).

La `send` méthode renvoie `Promise`un JavaScript . Le `Promise` est résolu lorsque le message a été envoyé au serveur. S’il y a une `Promise` erreur envoyant le message, le est rejeté avec le message d’erreur. Utilisez `then` les `catch` méthodes `Promise` et les méthodes en `await` soi pour traiter ces cas (ou syntaxe).

> [!NOTE]
> L’utilisation `send` n’attend pas que le serveur a reçu le message. Par conséquent, il n’est pas possible de retourner des données ou des erreurs à partir du serveur.

## <a name="call-client-methods-from-hub"></a>Appelez les méthodes des clients à partir d’un hub

Pour recevoir des messages du hub, [on](/javascript/api/%40aspnet/signalr/hubconnection#on) définissez `HubConnection`une méthode en utilisant la méthode sur le .

* Le nom de la méthode client JavaScript. Dans l’exemple suivant, `ReceiveMessage`le nom de la méthode est .
* Arguments le moyeu passe à la méthode. Dans l’exemple suivant, `message`la valeur de l’argument est .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Le code `connection.on` précédent s’exécute lorsque le code côté serveur l’appelle en utilisant la méthode [SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRdétermine la méthode du client à appeler en `SendAsync` faisant `connection.on`correspondre le nom de la méthode et les arguments définis dans et .

> [!NOTE]
> Comme une meilleure pratique, appelez `HubConnection` la `on`méthode [de départ](/javascript/api/%40aspnet/signalr/hubconnection#start) sur l’après . Cela garantit que vos gestionnaires sont enregistrés avant que tous les messages ne soient reçus.

## <a name="error-handling-and-logging"></a>Gestion et journalisation des erreurs

Enchaîner une `catch` méthode `start` jusqu’à la fin de la méthode pour gérer les erreurs côté client. Utilisez `console.error` pour les erreurs de sortie sur la console du navigateur.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Configurez le traçage des journaux côté client en passant un enregistreur et un type d’événement à enregistrer lorsque la connexion est effectuée. Les messages sont enregistrés avec le niveau de journal spécifié et plus élevé. Les niveaux de journal disponibles sont les suivants :

* `signalR.LogLevel.Error`&ndash; Messages d’erreur. Enregistre `Error` uniquement les messages.
* `signalR.LogLevel.Warning`&ndash; Messages d’avertissement sur les erreurs potentielles. `Warning`Journaux, et `Error` messages.
* `signalR.LogLevel.Information`&ndash; Messages de statut sans erreurs. Journaux `Information`, `Warning`, `Error` et des messages.
* `signalR.LogLevel.Trace`&ndash; Tracez des messages. Enregistre tout, y compris les données transportées entre le hub et le client.

Utilisez la méthode [configurer Encombrement](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) pour configurer le niveau du journal. Les messages sont connectés à la console du navigateur.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Reconnecter les clients

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconnectez-vous automatiquement

Le client JavaScript peut être configuré pour SignalR se reconnecter automatiquement en utilisant la `withAutomaticReconnect` méthode sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Il ne se reconnecte pas automatiquement par défaut.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Sans aucun paramètre, `withAutomaticReconnect()` configure le client pour attendre 0, 2, 10 et 30 secondes respectivement avant d’essayer chaque tentative de reconnexion, s’arrêtant après quatre tentatives infructueuses.

Avant de commencer toute `HubConnection` tentative de `HubConnectionState.Reconnecting` reconnexion, la transition à l’état et le feu de ses `onreconnecting` rappels au lieu de la transition à l’état et le `Disconnected` déclenchement de ses `onclose` rappels comme un `HubConnection` sans reconnecter automatique configuré. Cela donne l’occasion d’avertir les utilisateurs que la connexion a été perdue et de désactiver les éléments d’interface utilisateur.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Si le client se reconnecte avec `HubConnection` succès au cours `Connected` de ses `onreconnected` quatre premières tentatives, le sera la transition vers l’état et le feu de ses rappels. Cela donne l’occasion d’informer les utilisateurs que la connexion a été rétablie.

Étant donné que la connexion semble `connectionId` entièrement nouvelle sur `onreconnected` le serveur, un nouveau sera fourni au rappel.

> [!WARNING]
> Le `onreconnected` paramètre `connectionId` du rappel ne sera pas `HubConnection` défini si le a été configuré pour [sauter la négociation](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`ne configurera `HubConnection` pas les défaillances de démarrage initiales, de sorte que les échecs de démarrage doivent être manipulés manuellement :

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Si le client ne réussit pas à `HubConnection` se reconnecter `Disconnected` au cours de ses quatre premières tentatives, le sera la transition vers l’état et le feu de ses [rappels onclose.](/javascript/api/%40aspnet/signalr/hubconnection#onclose) Cela donne l’occasion d’informer les utilisateurs de la connexion a été définitivement perdu et recommande de rafraîchir la page:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Afin de configurer un nombre personnalisé de tentatives de `withAutomaticReconnect` reconnexion avant de déconnecter ou de modifier le calendrier de reconnexion, accepte une série de numéros représentant le retard en millisecondes à attendre avant de commencer chaque tentative de reconnexion.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

L’exemple précédent `HubConnection` configure le pour commencer à essayer se reconnecte immédiatement après la perte de la connexion. Cela est également vrai pour la configuration par défaut.

Si la première tentative de reconnexion échoue, la deuxième tentative de reconnexion commencera également immédiatement au lieu d’attendre 2 secondes comme elle le ferait dans la configuration par défaut.

Si la deuxième tentative de reconnexion échoue, la troisième tentative de reconnexion commencera en 10 secondes, ce qui est à nouveau comme la configuration par défaut.

Le comportement personnalisé diverge alors à nouveau du comportement par défaut en s’arrêtant après l’échec troisième tentative de reconnexion au lieu d’essayer une tentative de reconnexion de plus dans un autre 30 secondes comme il le ferait dans la configuration par défaut.

Si vous voulez encore plus de contrôle sur `withAutomaticReconnect` le calendrier et le `IRetryPolicy` nombre de tentatives `nextRetryDelayInMilliseconds`de reconnexion automatique, accepte un objet implémentant l’interface, qui a une méthode unique nommée .

`nextRetryDelayInMilliseconds`prend un seul argument `RetryContext`avec le type . Le `RetryContext` a trois `previousRetryCount` `elapsedMilliseconds` propriétés: , `retryReason` et qui sont un `number`, a `number` et un `Error` respectivement. Avant la première tentative `previousRetryCount` `elapsedMilliseconds` de reconnexion, les deux et seront nuls, et le `retryReason` sera l’erreur qui a causé la connexion à perdre. Après chaque tentative de `previousRetryCount` réessaie ratée, `elapsedMilliseconds` sera incrémenté par un, sera mis à jour `retryReason` pour refléter la quantité de temps passé à se reconnecter jusqu’à présent en millisecondes, et le sera l’erreur qui a causé la dernière tentative de reconnexion d’échouer.

`nextRetryDelayInMilliseconds`doit retourner soit un numéro représentant le nombre de millisecondes à attendre avant la prochaine tentative de reconnexion ou `null` si le `HubConnection` devrait cesser de se reconnecter.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Alternativement, vous pouvez écrire du code qui reconnectera votre client manuellement comme démontré dans [Manuellement reconnecter](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconnecter manuellement

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Avant 3.0, le client SignalR JavaScript ne se reconnecte pas automatiquement. Vous devez écrire du code qui reconnectera votre client manuellement.

::: moniker-end

Le code suivant démontre une approche de reconnexion manuelle typique :

1. Une fonction (dans ce `start` cas, la fonction) est créée pour démarrer la connexion.
1. Appelez `start` la fonction dans `onclose` le gestionnaire d’événement de la connexion.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Une implémentation du monde réel utiliserait un back-off exponentiel ou retenterait un certain nombre de fois avant d’abandonner.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Référence de l’API JavaScript](/javascript/api/?view=signalr-js-latest)
* [Tutoriel JavaScript](xref:tutorials/signalr)
* [Tutoriel WebPack et TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Moyeux](xref:signalr/hubs)
* [.NET client](xref:signalr/dotnet-client)
* [Publication dans Azure](xref:signalr/publish-to-azure-web-app)
* [Demandes d’origine croisée (CORS)](xref:security/cors)
* [Documentation SignalR sans serveur Azure Service](/azure/azure-signalr/signalr-concept-serverless-development-config)
