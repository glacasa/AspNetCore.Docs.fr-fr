---
title: Héberger et déployer Blazor ASP.net Core serveur
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application serveur à l’aide de ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: e69b91035c65739dde724330e83793c0b8b5481a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775152"
---
# <a name="host-and-deploy-blazor-server"></a>Héberger et Blazor déployer un serveur

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valeurs de configuration de l’hôte

Les applications serveur peuvent accepter des [valeurs de configuration d’hôte générique](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Déploiement

À l’aide du [ Blazor modèle](xref:blazor/hosting-models#blazor-server)d' Blazor Hébergement de serveur, est exécuté sur le serveur à partir d’une application ASP.net core. Les mises à jour de l’interface utilisateur, la gestion des événements et les [SignalR](xref:signalr/introduction) appels JavaScript sont gérés sur une connexion.

Un serveur web capable d’héberger une application ASP.NET Core est nécessaire. Visual Studio comprend`blazorserverside` le ** Blazor ** modèle de projet d’application serveur (modèle lors de l’utilisation de la commande [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Extensibilité

Planifiez un déploiement pour tirer le meilleur parti de l’infrastructure disponible pour Blazor une application serveur. Consultez les ressources suivantes pour résoudre Blazor l’extensibilité des applications serveur :

* [Notions de base Blazor des applications serveur](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a>Serveur de déploiement

Lorsque vous envisagez l’évolutivité d’un serveur unique (montée en puissance), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera en fonction des demandes des utilisateurs. La mémoire disponible sur le serveur affecte les éléments suivants :

* Nombre de circuits actifs qu’un serveur peut prendre en charge.
* Latence de l’interface utilisateur sur le client.

Pour obtenir des conseils sur la création Blazor d’applications serveur sécurisées et évolutives, consultez <xref:security/blazor/server/threat-mitigation>.

Chaque circuit utilise environ 250 Ko de mémoire pour une application de type *Hello World*minimale. La taille d’un circuit dépend du code de l’application et des exigences de maintenance d’état associées à chaque composant. Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ pour la planification de votre cible de déploiement : Si vous pensez que votre application prend en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire serveur vers l’application (ou ~ 273 Ko par utilisateur)

### <a name="signalr-configuration"></a>SignalRconfiguré

BlazorLes applications serveur utilisent SignalR ASP.net Core pour communiquer avec le navigateur. les conditions d’hébergement et de mise à Blazor [l’échelle de s’appliquent aux applications SignalR](xref:signalr/publish-to-azure-web-app) serveur.

Blazorfonctionne mieux lorsque vous utilisez WebSocket en tant SignalR que transport en raison d’une latence, d’une fiabilité et d’une [sécurité](xref:signalr/security)moindres. L’interrogation longue est utilisée SignalR par lorsque WebSocket n’est pas disponible ou lorsque l’application est configurée explicitement pour utiliser une interrogation longue. Lors du déploiement sur Azure App Service, configurez l’application pour qu’elle utilise WebSockets dans les paramètres Portail Azure pour le service. Pour plus d’informations sur la configuration de l’application pour Azure App service, consultez les [ SignalR instructions de publication](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Service SignalR Azure

Nous vous recommandons d’utiliser le [service Azure SignalR ](/azure/azure-signalr) pour Blazor les applications serveur. Le service permet la mise à l’échelle Blazor d’une application serveur vers un grand nombre SignalR de connexions simultanées. En outre, la SignalR portée mondiale et les centres de données haute performance du service contribuent de manière significative à réduire la latence en raison de la géographie. Pour configurer une application (et éventuellement approvisionner) le service SignalR Azure :

1. Activez le service pour prendre en charge les *sessions rémanentes*, où les clients sont [redirigés vers le même serveur lors du prérendu](xref:blazor/hosting-models#connection-to-the-server). Définissez l' `ServerStickyMode` option ou la valeur de `Required`configuration sur. En règle générale, une application crée la configuration à l’aide de l' **une** des approches suivantes :

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuration (utilisez l' **une** des approches suivantes) :
  
     * *appSettings. JSON*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Les**paramètres d’application** de **configuration** > de l’app service dans le portail Azure `Azure:SignalR:ServerStickyMode`(**nom**: `Required`, **valeur**:).

1. Créez un profil de publication Azure Apps dans Visual Studio pour Blazor l’application serveur.
1. Ajoutez la dépendance du **service Azure SignalR ** au profil. Si l’abonnement Azure n’a pas d’instance de service SignalR Azure préexistante à attribuer à l’application, sélectionnez **créer une SignalR nouvelle instance de service Azure** pour approvisionner une nouvelle instance de service.
1. Publiez l’application dans Azure.

#### <a name="iis"></a>IIS

Lorsque vous utilisez IIS, activez :

* [WebSocket sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessions rémanentes avec application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Créez une définition d’entrée avec les [Annotations Kubernetes suivantes pour les sessions rémanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

Pour SignalR que WebSocket fonctionne correctement, vérifiez que les en-têtes `Upgrade` et `Connection` du proxy sont définis sur les valeurs suivantes et qu' `$connection_upgrade` ils sont mappés à l’un ou l’autre des éléments suivants :

* Valeur d’en-tête de mise à niveau par défaut.
* `close`Lorsque l’en-tête de mise à niveau est manquant ou vide.

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
* [Proxy WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Mesurer la latence du réseau

L' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) peut être utilisée pour mesurer la latence du réseau, comme le montre l’exemple suivant :

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

Pour une expérience d’interface utilisateur raisonnable, nous recommandons une latence d’interface utilisateur soutenue de 250 ms ou moins.
