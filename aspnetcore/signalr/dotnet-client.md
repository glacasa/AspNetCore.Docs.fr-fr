---
title: Client SignalR .net ASP.net Core
author: bradygaster
description: Informations sur le client SignalR ASP.net core .net
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 77d7eb81abc4ec7a6f4f15bbe5d96cedc64cb330
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767211"
---
# <a name="aspnet-core-signalr-net-client"></a>Client ASP.NET Core Signalr .NET

La bibliothèque cliente ASP.NET Core Signalr .NET vous permet de communiquer avec les hubs Signalr à partir d’applications .NET.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple de code dans cet article est une application WPF qui utilise le client ASP.NET Core Signalr .NET.

## <a name="install-the-signalr-net-client-package"></a>Installer le package du client .NET Signalr

Le package [Microsoft. AspNetCore. signalr. client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) est requis pour que les clients .net se connectent aux hubs signalr.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour installer la bibliothèque cliente, exécutez la commande suivante dans la fenêtre **console du gestionnaire de package** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Pour installer la bibliothèque cliente, exécutez la commande suivante dans une interface de commande :

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Se connecter à un concentrateur

Pour établir une connexion, créez un `HubConnectionBuilder` et appelez `Build`. L’URL du concentrateur, le protocole, le type de transport, le niveau de journalisation, les en-têtes et d’autres options peuvent être configurés lors de la création d’une connexion. Configurez toutes les options requises en insérant `HubConnectionBuilder` les méthodes `Build`dans. Démarrez la connexion avec `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Gérer la connexion perdue

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Se reconnecter automatiquement

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> Peut être configuré pour se reconnecter automatiquement à l' `WithAutomaticReconnect` aide de la <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>méthode sur le. Par défaut, il ne se reconnectera pas automatiquement.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Sans aucun paramètre, `WithAutomaticReconnect()` configure le client pour attendre 0, 2, 10 et 30 secondes, respectivement, avant d’essayer chaque tentative de reconnexion, en s’arrêtant après quatre tentatives ayant échoué.

Avant de commencer les tentatives de reconnexion `HubConnection` , le passe à `HubConnectionState.Reconnecting` l’État et déclenche `Reconnecting` l’événement.  Cela permet d’avertir les utilisateurs que la connexion a été perdue et de désactiver les éléments d’interface utilisateur. Les applications non interactives peuvent commencer à mettre en file d’attente ou à déposer des messages.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Si le client se reconnecte correctement dans les quatre premières tentatives, `HubConnection` le passe à l' `Connected` État et déclenche l' `Reconnected` événement. Cela permet d’informer les utilisateurs que la connexion a été rétablie et de défiler tous les messages mis en file d’attente.

Étant donné que la connexion semble entièrement nouvelle sur le serveur, `ConnectionId` une nouvelle est fournie aux `Reconnected` gestionnaires d’événements.

> [!WARNING]
> Le `Reconnected` paramètre du gestionnaire `connectionId` d’événements sera null si a `HubConnection` été configuré pour [ignorer la négociation](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()`ne configure `HubConnection` pas le pour réessayer les échecs de démarrage initial. les échecs de démarrage doivent donc être gérés manuellement :

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Si le client ne se reconnecte pas correctement dans les quatre premières `HubConnection` tentatives, le passe `Disconnected` à l’État et <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> déclenche l’événement. Cela offre la possibilité de tenter de redémarrer manuellement la connexion ou d’informer les utilisateurs que la connexion a été définitivement perdue.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Pour configurer un nombre personnalisé de tentatives de reconnexion avant de vous déconnecter ou de modifier le délai de reconnexion, `WithAutomaticReconnect` accepte un tableau de nombres représentant le délai en millisecondes à attendre avant de démarrer chaque tentative de reconnexion.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

L’exemple précédent configure le pour `HubConnection` qu’il commence à tenter de se reconnecter immédiatement après la perte de la connexion. Cela est également vrai pour la configuration par défaut.

Si la première tentative de reconnexion échoue, la deuxième tentative de reconnexion démarre également immédiatement au lieu d’attendre 2 secondes comme dans la configuration par défaut.

Si la deuxième tentative de reconnexion échoue, la troisième tentative de reconnexion démarrera dans 10 secondes, ce qui revient à la configuration par défaut.

Le comportement personnalisé s’est ensuite dévergé du comportement par défaut en s’arrêtant après le troisième échec de tentative de reconnexion. Dans la configuration par défaut, il y aura une autre tentative de reconnexion dans 30 secondes.

Si vous souhaitez encore plus de contrôle sur le minutage et le nombre de tentatives de reconnexion automatique, `WithAutomaticReconnect` accepte un `IRetryPolicy` objet qui implémente l’interface, qui `NextRetryDelay`a une méthode unique nommée.

`NextRetryDelay`accepte un seul argument avec le type `RetryContext`. Le `RetryContext` a trois propriétés : `PreviousRetryCount`, `ElapsedTime` et `RetryReason`, qui sont `long` `Exception` respectivement un, `TimeSpan` un et un. Avant la première tentative de reconnexion, `PreviousRetryCount` et `ElapsedTime` sera égal à zéro, et `RetryReason` sera l’exception qui a provoqué la perte de la connexion. Après chaque nouvelle tentative d’échec `PreviousRetryCount` , sera incrémenté d’une unité `ElapsedTime` , sera mis à jour pour refléter le temps passé à se reconnecter jusqu’à présent, `RetryReason` et sera l’exception qui a provoqué l’échec de la dernière tentative de reconnexion.

`NextRetryDelay`doit retourner une valeur TimeSpan représentant le délai d’attente avant la tentative de reconnexion suivante `null` ou si `HubConnection` doit arrêter la reconnexion.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Vous pouvez également écrire du code qui reconnectera votre client manuellement, comme illustré dans [reconnexion manuelle](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconnexion manuelle

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Avant le 3,0, le client .NET pour SignalR ne se reconnecte pas automatiquement. Vous devez écrire du code qui reconnectera votre client manuellement.

::: moniker-end

Utilisez l' <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> événement pour répondre à une connexion perdue. Par exemple, vous souhaiterez peut-être automatiser la reconnexion.

L' `Closed` événement requiert un délégué qui retourne un `Task`, ce qui permet au code Async de s' `async void`exécuter sans utiliser. Pour satisfaire la signature du délégué dans `Closed` un gestionnaire d’événements qui s’exécute de `Task.CompletedTask`façon synchrone, retournez :

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

La principale raison de la prise en charge de Async est que vous pouvez redémarrer la connexion. Le démarrage d’une connexion est une action asynchrone.

Dans un `Closed` gestionnaire qui redémarre la connexion, pensez à attendre un certain délai aléatoire pour empêcher la surcharge du serveur, comme illustré dans l’exemple suivant :

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Appeler les méthodes de concentrateur à partir du client

`InvokeAsync`appelle des méthodes sur le concentrateur. Transmettez le nom de la méthode de concentrateur et les arguments définis `InvokeAsync`dans la méthode de concentrateur à. SignalRest asynchrone. Utilisez `async` donc et `await` lorsque vous effectuez les appels.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

La `InvokeAsync` méthode retourne un `Task` qui se termine lorsque la méthode serveur retourne. La valeur de retour, le cas échéant, est fournie comme résultat de `Task`. Toute exception levée par la méthode sur le serveur génère une erreur `Task`. Utilisez `await` la syntaxe pour attendre la fin de la méthode serveur `try...catch` et la syntaxe pour gérer les erreurs.

La `SendAsync` méthode retourne un `Task` qui se termine lorsque le message a été envoyé au serveur. Aucune valeur de retour n’est fournie `Task` , car cela n’attend pas la fin de la méthode serveur. Toutes les exceptions levées sur le client lors de l’envoi du message `Task`génèrent une erreur. Utilisez `await` la `try...catch` syntaxe et pour gérer les erreurs d’envoi.

> [!NOTE]
> Si vous utilisez le service SignalR Azure en *mode sans serveur*, vous ne pouvez pas appeler les méthodes de concentrateur à partir d’un client. Pour plus d’informations, consultez la [ SignalR documentation du service](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Appeler des méthodes clientes à partir du Hub

Définissez les méthodes que le Hub `connection.On` appelle en utilisant après la génération, mais avant de démarrer la connexion.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Le code précédent dans `connection.On` s’exécute quand le code côté serveur l’appelle à `SendAsync` l’aide de la méthode.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Gestion et journalisation des erreurs

Gérer les erreurs à l’aide d’une instruction try-catch. Inspectez `Exception` l’objet pour déterminer l’action appropriée à entreprendre après l’exécution d’une erreur.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Hubs](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)
* [Publication dans Azure](xref:signalr/publish-to-azure-web-app)
* [Documentation SignalR sans serveur de service Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
