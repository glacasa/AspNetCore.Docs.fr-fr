---
title: Hébergez et déployez ASP.NET Serveur de base Blazor
author: guardrex
description: Découvrez comment héberger Blazor et déployer une application Server à l’aide de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025425"
---
# <a name="host-and-deploy-opno-locblazor-server"></a>Hébergez Blazor et déployez Server

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valeurs de configuration de l’hôte

Les applications serveur peuvent accepter [les valeurs de configuration d’hôte générique](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Déploiement

L’utilisation du [ Blazor modèle d’hébergement Server](xref:blazor/hosting-models#blazor-server), Blazor est exécutée sur le serveur à partir d’une application ASP.NET Core. Les mises à jour de l’interface utilisateur, [SignalR](xref:signalr/introduction) la gestion des événements et les appels JavaScript sont traités sur une connexion.

Un serveur web capable d’héberger une application ASP.NET Core est nécessaire. Visual Studio inclut le modèle`blazorserverside` ** Blazor de** projet Server App (modèle lors de l’utilisation de la nouvelle commande [dotnet).](/dotnet/core/tools/dotnet-new)

## <a name="scalability"></a>Extensibilité

Planifiez un déploiement pour tirer le meilleur Blazor parti de l’infrastructure disponible pour une application Server. Voir les ressources Blazor suivantes pour répondre à l’évolutivité de l’application Server :

* [Principes fondamentaux des Blazor applications Server](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Serveur de déploiement

Lorsque l’on considère l’évolutivité d’un seul serveur (à l’échelle), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera à mesure que les demandes de l’utilisateur augmentent. La mémoire disponible sur le serveur affecte le :

* Nombre de circuits actifs qu’un serveur peut prendre en charge.
* Latence de l’assurance-chômage sur le client.

Pour obtenir des conseils sur Blazor la création <xref:security/blazor/server>d’applications serveur sécurisées et évolutives, voir .

Chaque circuit utilise environ 250 KB de mémoire pour une application minimale de style *Hello World.* La taille d’un circuit dépend du code de l’application et des exigences de maintenance de l’État associées à chaque composant. Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ dans la planification de votre objectif de déploiement : si vous vous attendez à ce que votre application puisse prendre en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire du serveur à l’application (ou 273 KB par utilisateur).

### <a name="opno-locsignalr-configuration"></a>SignalRConfiguration

BlazorLes applications serveur utilisent SignalR ASP.NET Core pour communiquer avec le navigateur. les conditions d’hébergement et Blazor de mise à [l’échelle s’appliquent aux applications SignalR](xref:signalr/publish-to-azure-web-app) Server.

Blazorfonctionne mieux lors de l’utilisation de WebSockets comme le SignalR transport en raison de la latence plus faible, la fiabilité et la [sécurité](xref:signalr/security). Long Polling est SignalR utilisé lorsque WebSockets n’est pas disponible ou lorsque l’application est explicitement configurée pour utiliser Long Polling. Lors du déploiement vers Azure App Service, configurez l’application pour utiliser WebSockets dans les paramètres du portail Azure pour le service. Pour plus de détails sur la configuration de [ SignalR ](xref:signalr/publish-to-azure-web-app)l’application pour Azure App Service, consultez les directives de publication .

#### <a name="azure-opno-locsignalr-service"></a>Azure SignalR Service

Nous vous recommandons d’utiliser le [service Azure SignalR ](/azure/azure-signalr) pour Blazor les applications Server. Le service permet d’intensifier Blazor une application Server à SignalR un grand nombre de connexions simultanées. En outre, SignalR la portée mondiale et les centres de données de haute performance du service aident considérablement à réduire la latence due à la géographie. Pour configurer une application (et une SignalR disposition optionnelle) le service Azure :

1. Activez le service pour prendre en charge *les sessions collantes,* où les clients sont [redirigés vers le même serveur lors du précommande](xref:blazor/hosting-models#connection-to-the-server). Définissez `ServerStickyMode` la valeur `Required`de l’option ou de la configuration à . En règle générale, une application crée la configuration à **l’aide d’une** des approches suivantes :

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuration (utiliser **l’une** des approches suivantes) :
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Les paramètres **d’application configuration** > **Application settings** du service d’application dans le portail Azure (**Nom**: `Azure:SignalR:ServerStickyMode`, **Valeur**: `Required`).

1. Créez un profil de publication Azure Blazor Apps dans Visual Studio pour l’application Server.
1. Ajoutez la dépendance **au service Azure SignalR ** au profil. Si l’abonnement Azure n’a pas SignalR d’instance de service Azure préexistant à attribuer à l’application, **sélectionnez Créer une nouvelle instance de service Azure SignalR ** pour fournir une nouvelle instance de service.
1. Publiez l’application dans Azure.

#### <a name="iis"></a>IIS

Lors de l’utilisation de l’IIS, activez :

* [WebSockets sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Séances collantes avec itinéraire de demande d’application](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Créez une définition d’entrée avec les [annotations suivantes de Kubernetes pour des sessions collantes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>Linux avec Nginx

Pour SignalR que WebSockets fonctionne correctement, confirmez `Upgrade` `Connection` que les proxy et les `$connection_upgrade` en-têtes sont réglés sur les valeurs suivantes et que cela est cartographié à l’un ou l’autre:

* La valeur d’en-tête De mise à niveau par défaut.
* `close`lorsque l’en-tête De mise à niveau est manquant ou vide.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Pour plus d’informations, consultez les articles suivants :

* [NGINX en tant que proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxying WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Mesurer la latence du réseau

[JS interop](xref:blazor/call-javascript-from-dotnet) peut être utilisé pour mesurer la latence du réseau, comme le montre l’exemple suivant :

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

Pour une expérience raisonnable de l’assurance-chômage, nous recommandons une latence d’assurance-chômage soutenue de 250ms ou moins.
