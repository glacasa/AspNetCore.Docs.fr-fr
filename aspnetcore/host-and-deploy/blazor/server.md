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
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="3a1d7-103">Hébergez Blazor et déployez Server</span><span class="sxs-lookup"><span data-stu-id="3a1d7-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="3a1d7-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3a1d7-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="3a1d7-105">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="3a1d7-105">Host configuration values</span></span>

<span data-ttu-id="3a1d7-106">Les applications serveur peuvent accepter [les valeurs de configuration d’hôte générique](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="3a1d7-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="3a1d7-107">Déploiement</span><span class="sxs-lookup"><span data-stu-id="3a1d7-107">Deployment</span></span>

<span data-ttu-id="3a1d7-108">L’utilisation du [ Blazor modèle d’hébergement Server](xref:blazor/hosting-models#blazor-server), Blazor est exécutée sur le serveur à partir d’une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="3a1d7-109">Les mises à jour de l’interface utilisateur, [SignalR](xref:signalr/introduction) la gestion des événements et les appels JavaScript sont traités sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="3a1d7-110">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="3a1d7-111">Visual Studio inclut le modèle`blazorserverside` \*\* Blazor de\*\* projet Server App (modèle lors de l’utilisation de la nouvelle commande [dotnet).](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="3a1d7-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="3a1d7-112">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="3a1d7-112">Scalability</span></span>

<span data-ttu-id="3a1d7-113">Planifiez un déploiement pour tirer le meilleur Blazor parti de l’infrastructure disponible pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="3a1d7-114">Voir les ressources Blazor suivantes pour répondre à l’évolutivité de l’application Server :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="3a1d7-115">[Principes fondamentaux des Blazor applications Server](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="3a1d7-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="3a1d7-116">Serveur de déploiement</span><span class="sxs-lookup"><span data-stu-id="3a1d7-116">Deployment server</span></span>

<span data-ttu-id="3a1d7-117">Lorsque l’on considère l’évolutivité d’un seul serveur (à l’échelle), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera à mesure que les demandes de l’utilisateur augmentent.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="3a1d7-118">La mémoire disponible sur le serveur affecte le :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="3a1d7-119">Nombre de circuits actifs qu’un serveur peut prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="3a1d7-120">Latence de l’assurance-chômage sur le client.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-120">UI latency on the client.</span></span>

<span data-ttu-id="3a1d7-121">Pour obtenir des conseils sur Blazor la création <xref:security/blazor/server>d’applications serveur sécurisées et évolutives, voir .</span><span class="sxs-lookup"><span data-stu-id="3a1d7-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="3a1d7-122">Chaque circuit utilise environ 250 KB de mémoire pour une application minimale de style *Hello World.*</span><span class="sxs-lookup"><span data-stu-id="3a1d7-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="3a1d7-123">La taille d’un circuit dépend du code de l’application et des exigences de maintenance de l’État associées à chaque composant.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="3a1d7-124">Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ dans la planification de votre objectif de déploiement : si vous vous attendez à ce que votre application puisse prendre en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire du serveur à l’application (ou 273 KB par utilisateur).</span><span class="sxs-lookup"><span data-stu-id="3a1d7-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a>SignalR<span data-ttu-id="3a1d7-125">Configuration</span><span class="sxs-lookup"><span data-stu-id="3a1d7-125"> configuration</span></span>

Blazor<span data-ttu-id="3a1d7-126">Les applications serveur utilisent SignalR ASP.NET Core pour communiquer avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="3a1d7-127">les conditions d’hébergement et Blazor de mise à [l’échelle s’appliquent aux applications SignalR](xref:signalr/publish-to-azure-web-app) Server.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="3a1d7-128">fonctionne mieux lors de l’utilisation de WebSockets comme le SignalR transport en raison de la latence plus faible, la fiabilité et la [sécurité](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="3a1d7-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="3a1d7-129">Long Polling est SignalR utilisé lorsque WebSockets n’est pas disponible ou lorsque l’application est explicitement configurée pour utiliser Long Polling.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="3a1d7-130">Lors du déploiement vers Azure App Service, configurez l’application pour utiliser WebSockets dans les paramètres du portail Azure pour le service.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="3a1d7-131">Pour plus de détails sur la configuration de [ SignalR ](xref:signalr/publish-to-azure-web-app)l’application pour Azure App Service, consultez les directives de publication .</span><span class="sxs-lookup"><span data-stu-id="3a1d7-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="3a1d7-132">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="3a1d7-132">Azure SignalR Service</span></span>

<span data-ttu-id="3a1d7-133">Nous vous recommandons d’utiliser le [service Azure SignalR ](/azure/azure-signalr) pour Blazor les applications Server.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="3a1d7-134">Le service permet d’intensifier Blazor une application Server à SignalR un grand nombre de connexions simultanées.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="3a1d7-135">En outre, SignalR la portée mondiale et les centres de données de haute performance du service aident considérablement à réduire la latence due à la géographie.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="3a1d7-136">Pour configurer une application (et une SignalR disposition optionnelle) le service Azure :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="3a1d7-137">Activez le service pour prendre en charge *les sessions collantes,* où les clients sont [redirigés vers le même serveur lors du précommande](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="3a1d7-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="3a1d7-138">Définissez `ServerStickyMode` la valeur `Required`de l’option ou de la configuration à .</span><span class="sxs-lookup"><span data-stu-id="3a1d7-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="3a1d7-139">En règle générale, une application crée la configuration à **l’aide d’une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="3a1d7-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3a1d7-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="3a1d7-141">Configuration (utiliser **l’une** des approches suivantes) :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="3a1d7-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3a1d7-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="3a1d7-143">Les paramètres **d’application configuration** > **Application settings** du service d’application dans le portail Azure (**Nom**: `Azure:SignalR:ServerStickyMode`, **Valeur**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="3a1d7-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="3a1d7-144">Créez un profil de publication Azure Blazor Apps dans Visual Studio pour l’application Server.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="3a1d7-145">Ajoutez la dépendance \*\*au service Azure SignalR \*\* au profil.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="3a1d7-146">Si l’abonnement Azure n’a pas SignalR d’instance de service Azure préexistant à attribuer à l’application, \*\*sélectionnez Créer une nouvelle instance de service Azure SignalR \*\* pour fournir une nouvelle instance de service.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="3a1d7-147">Publiez l’application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="3a1d7-148">IIS</span><span class="sxs-lookup"><span data-stu-id="3a1d7-148">IIS</span></span>

<span data-ttu-id="3a1d7-149">Lors de l’utilisation de l’IIS, activez :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-149">When using IIS, enable:</span></span>

* <span data-ttu-id="3a1d7-150">[WebSockets sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="3a1d7-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="3a1d7-151">[Séances collantes avec itinéraire de demande d’application](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="3a1d7-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="3a1d7-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="3a1d7-152">Kubernetes</span></span>

<span data-ttu-id="3a1d7-153">Créez une définition d’entrée avec les [annotations suivantes de Kubernetes pour des sessions collantes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="3a1d7-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="3a1d7-154">Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="3a1d7-154">Linux with Nginx</span></span>

<span data-ttu-id="3a1d7-155">Pour SignalR que WebSockets fonctionne correctement, confirmez `Upgrade` `Connection` que les proxy et les `$connection_upgrade` en-têtes sont réglés sur les valeurs suivantes et que cela est cartographié à l’un ou l’autre:</span><span class="sxs-lookup"><span data-stu-id="3a1d7-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="3a1d7-156">La valeur d’en-tête De mise à niveau par défaut.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="3a1d7-157">`close`lorsque l’en-tête De mise à niveau est manquant ou vide.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="3a1d7-158">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="3a1d7-159">NGINX en tant que proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="3a1d7-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="3a1d7-160">Proxying WebSocket</span><span class="sxs-lookup"><span data-stu-id="3a1d7-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="3a1d7-161">Mesurer la latence du réseau</span><span class="sxs-lookup"><span data-stu-id="3a1d7-161">Measure network latency</span></span>

<span data-ttu-id="3a1d7-162">[JS interop](xref:blazor/call-javascript-from-dotnet) peut être utilisé pour mesurer la latence du réseau, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="3a1d7-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="3a1d7-163">Pour une expérience raisonnable de l’assurance-chômage, nous recommandons une latence d’assurance-chômage soutenue de 250ms ou moins.</span><span class="sxs-lookup"><span data-stu-id="3a1d7-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
