---
title: ASP.NET Core SignalR des hôtes dans les services d’arrière-plan
author: bradygaster
description: Découvrez comment envoyer des messages à SignalR des clients à partir de classes BackgroundService .net core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777291"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR des hôtes dans les services d’arrière-plan

Par [Brady Gaster](https://twitter.com/bradygaster)

Cet article fournit des conseils pour :

* Hébergement SignalR de hubs à l’aide d’un processus de travail en arrière-plan hébergé avec ASP.net core.
* Envoi de messages à des clients connectés à partir d’un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).net core.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Activer SignalR au démarrage

::: moniker range=">= aspnetcore-3.0"

L’hébergement SignalR de ASP.net Core hubs dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un concentrateur dans une application Web ASP.net core. Dans la `Startup.ConfigureServices` méthode, l' `services.AddSignalR` appel de ajoute les services requis à la couche d’Injection de dépendances ASP.net Core SignalR(di) pour prendre en charge. Dans `Startup.Configure`, la `MapHub` méthode est appelée dans le `UseEndpoints` rappel pour connecter les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

L’hébergement SignalR de ASP.net Core hubs dans le contexte d’un processus de travail en arrière-plan est identique à l’hébergement d’un concentrateur dans une application Web ASP.net core. Dans la `Startup.ConfigureServices` méthode, l' `services.AddSignalR` appel de ajoute les services requis à la couche d’Injection de dépendances ASP.net Core SignalR(di) pour prendre en charge. Dans `Startup.Configure`, la `UseSignalR` méthode est appelée pour connecter le ou les points de terminaison de concentrateur dans le pipeline de requête ASP.net core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

Dans l’exemple précédent, la `ClockHub` classe implémente la `Hub<T>` classe pour créer un concentrateur fortement typé. `ClockHub` A été configuré dans la `Startup` classe pour répondre aux demandes au niveau du point de terminaison `/hubs/clock`.

Pour plus d’informations sur les hubs fortement typés, consultez [utiliser des hubs SignalR dans pour ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Cette fonctionnalité n’est pas limitée à la classe [\<Hub t>](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Toute classe qui hérite de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), telle que [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), fonctionne également.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

L’interface utilisée par le fortement typé `ClockHub` est l' `IClock` interface.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Appeler un SignalR Hub à partir d’un service d’arrière-plan

Au démarrage, la `Worker` classe, a `BackgroundService`est activée à l' `AddHostedService`aide de.

```csharp
services.AddHostedService<Worker>();
```

Étant SignalR donné que est également activé pendant `Startup` la phase, dans lequel chaque concentrateur est attaché à un point de terminaison individuel dans le pipeline de requête HTTP de ASP.net Core `IHubContext<T>` , chaque concentrateur est représenté par un sur le serveur. À l’aide des fonctionnalités DI de ASP.NET Core, les autres classes instanciées par la `BackgroundService` couche d’hébergement, comme les classes Razor , les classes de contrôleur MVC ou les modèles de page, peuvent recevoir des `IHubContext<ClockHub, IClock>` références aux hubs côté serveur en acceptant des instances de lors de la construction.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Étant donné `ExecuteAsync` que la méthode est appelée de manière itérative dans le service d’arrière-plan, la date et l’heure actuelles du serveur sont `ClockHub`envoyées aux clients connectés à l’aide de.

## <a name="react-to-signalr-events-with-background-services"></a>Réagir aux SignalR événements avec les services d’arrière-plan

À l’instar d’une application à page unique SignalR utilisant le client JavaScript pour ou une application de bureau .NET <xref:signalr/dotnet-client>, vous `BackgroundService` pouvez `IHostedService` utiliser le à l’aide du, une SignalR implémentation ou peut également être utilisée pour se connecter aux hubs et répondre aux événements.

La `ClockHubClient` classe implémente à la `IClock` fois l’interface `IHostedService` et l’interface. De cette façon, il peut être `Startup` activé lors de l’exécution en continu et répondre aux événements de concentrateur à partir du serveur.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Pendant l’initialisation `ClockHubClient` , crée une instance de `HubConnection` et active la `IClock.ShowTime` méthode en tant que gestionnaire de l’événement du `ShowTime` concentrateur.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Dans l' `IHostedService.StartAsync` implémentation, le `HubConnection` est démarré de manière asynchrone.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Pendant la `IHostedService.StopAsync` méthode, le `HubConnection` est supprimé de manière asynchrone.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Ressources supplémentaires

* [Prise en main](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publication dans Azure](xref:signalr/publish-to-azure-web-app)
* [Hubs fortement typés](xref:signalr/hubs#strongly-typed-hubs)
