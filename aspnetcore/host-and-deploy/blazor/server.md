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
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="4da95-103">Héberger et Blazor déployer un serveur</span><span class="sxs-lookup"><span data-stu-id="4da95-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="4da95-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4da95-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="4da95-105">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="4da95-105">Host configuration values</span></span>

<span data-ttu-id="4da95-106">Les applications serveur peuvent accepter des [valeurs de configuration d’hôte générique](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="4da95-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="4da95-107">Déploiement</span><span class="sxs-lookup"><span data-stu-id="4da95-107">Deployment</span></span>

<span data-ttu-id="4da95-108">À l’aide du [ Blazor modèle](xref:blazor/hosting-models#blazor-server)d' Blazor Hébergement de serveur, est exécuté sur le serveur à partir d’une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="4da95-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="4da95-109">Les mises à jour de l’interface utilisateur, la gestion des événements et les [SignalR](xref:signalr/introduction) appels JavaScript sont gérés sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="4da95-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="4da95-110">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="4da95-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="4da95-111">Visual Studio comprend`blazorserverside` le \*\* Blazor \*\* modèle de projet d’application serveur (modèle lors de l’utilisation de la commande [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="4da95-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="4da95-112">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="4da95-112">Scalability</span></span>

<span data-ttu-id="4da95-113">Planifiez un déploiement pour tirer le meilleur parti de l’infrastructure disponible pour Blazor une application serveur.</span><span class="sxs-lookup"><span data-stu-id="4da95-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="4da95-114">Consultez les ressources suivantes pour résoudre Blazor l’extensibilité des applications serveur :</span><span class="sxs-lookup"><span data-stu-id="4da95-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="4da95-115">[Notions de base Blazor des applications serveur](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="4da95-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="4da95-116">Serveur de déploiement</span><span class="sxs-lookup"><span data-stu-id="4da95-116">Deployment server</span></span>

<span data-ttu-id="4da95-117">Lorsque vous envisagez l’évolutivité d’un serveur unique (montée en puissance), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera en fonction des demandes des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="4da95-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="4da95-118">La mémoire disponible sur le serveur affecte les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="4da95-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="4da95-119">Nombre de circuits actifs qu’un serveur peut prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="4da95-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="4da95-120">Latence de l’interface utilisateur sur le client.</span><span class="sxs-lookup"><span data-stu-id="4da95-120">UI latency on the client.</span></span>

<span data-ttu-id="4da95-121">Pour obtenir des conseils sur la création Blazor d’applications serveur sécurisées et évolutives, consultez <xref:security/blazor/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="4da95-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="4da95-122">Chaque circuit utilise environ 250 Ko de mémoire pour une application de type *Hello World*minimale.</span><span class="sxs-lookup"><span data-stu-id="4da95-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="4da95-123">La taille d’un circuit dépend du code de l’application et des exigences de maintenance d’état associées à chaque composant.</span><span class="sxs-lookup"><span data-stu-id="4da95-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="4da95-124">Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ pour la planification de votre cible de déploiement : Si vous pensez que votre application prend en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire serveur vers l’application (ou ~ 273 Ko par utilisateur)</span><span class="sxs-lookup"><span data-stu-id="4da95-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="4da95-125">configuré</span><span class="sxs-lookup"><span data-stu-id="4da95-125"> configuration</span></span>

Blazor<span data-ttu-id="4da95-126">Les applications serveur utilisent SignalR ASP.net Core pour communiquer avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="4da95-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="4da95-127">les conditions d’hébergement et de mise à Blazor [l’échelle de s’appliquent aux applications SignalR](xref:signalr/publish-to-azure-web-app) serveur.</span><span class="sxs-lookup"><span data-stu-id="4da95-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="4da95-128">fonctionne mieux lorsque vous utilisez WebSocket en tant SignalR que transport en raison d’une latence, d’une fiabilité et d’une [sécurité](xref:signalr/security)moindres.</span><span class="sxs-lookup"><span data-stu-id="4da95-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="4da95-129">L’interrogation longue est utilisée SignalR par lorsque WebSocket n’est pas disponible ou lorsque l’application est configurée explicitement pour utiliser une interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="4da95-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="4da95-130">Lors du déploiement sur Azure App Service, configurez l’application pour qu’elle utilise WebSockets dans les paramètres Portail Azure pour le service.</span><span class="sxs-lookup"><span data-stu-id="4da95-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="4da95-131">Pour plus d’informations sur la configuration de l’application pour Azure App service, consultez les [ SignalR instructions de publication](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="4da95-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="4da95-132">Service SignalR Azure</span><span class="sxs-lookup"><span data-stu-id="4da95-132">Azure SignalR Service</span></span>

<span data-ttu-id="4da95-133">Nous vous recommandons d’utiliser le [service Azure SignalR ](/azure/azure-signalr) pour Blazor les applications serveur.</span><span class="sxs-lookup"><span data-stu-id="4da95-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="4da95-134">Le service permet la mise à l’échelle Blazor d’une application serveur vers un grand nombre SignalR de connexions simultanées.</span><span class="sxs-lookup"><span data-stu-id="4da95-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="4da95-135">En outre, la SignalR portée mondiale et les centres de données haute performance du service contribuent de manière significative à réduire la latence en raison de la géographie.</span><span class="sxs-lookup"><span data-stu-id="4da95-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="4da95-136">Pour configurer une application (et éventuellement approvisionner) le service SignalR Azure :</span><span class="sxs-lookup"><span data-stu-id="4da95-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="4da95-137">Activez le service pour prendre en charge les *sessions rémanentes*, où les clients sont [redirigés vers le même serveur lors du prérendu](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="4da95-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="4da95-138">Définissez l' `ServerStickyMode` option ou la valeur de `Required`configuration sur.</span><span class="sxs-lookup"><span data-stu-id="4da95-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="4da95-139">En règle générale, une application crée la configuration à l’aide de l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="4da95-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="4da95-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4da95-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="4da95-141">Configuration (utilisez l' **une** des approches suivantes) :</span><span class="sxs-lookup"><span data-stu-id="4da95-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="4da95-142">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="4da95-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="4da95-143">Les**paramètres d’application** de **configuration** > de l’app service dans le portail Azure `Azure:SignalR:ServerStickyMode`(**nom**: `Required`, **valeur**:).</span><span class="sxs-lookup"><span data-stu-id="4da95-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="4da95-144">Créez un profil de publication Azure Apps dans Visual Studio pour Blazor l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="4da95-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="4da95-145">Ajoutez la dépendance du \*\*service Azure SignalR \*\* au profil.</span><span class="sxs-lookup"><span data-stu-id="4da95-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="4da95-146">Si l’abonnement Azure n’a pas d’instance de service SignalR Azure préexistante à attribuer à l’application, sélectionnez **créer une SignalR nouvelle instance de service Azure** pour approvisionner une nouvelle instance de service.</span><span class="sxs-lookup"><span data-stu-id="4da95-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="4da95-147">Publiez l’application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="4da95-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="4da95-148">IIS</span><span class="sxs-lookup"><span data-stu-id="4da95-148">IIS</span></span>

<span data-ttu-id="4da95-149">Lorsque vous utilisez IIS, activez :</span><span class="sxs-lookup"><span data-stu-id="4da95-149">When using IIS, enable:</span></span>

* <span data-ttu-id="4da95-150">[WebSocket sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="4da95-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="4da95-151">[Sessions rémanentes avec application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="4da95-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="4da95-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="4da95-152">Kubernetes</span></span>

<span data-ttu-id="4da95-153">Créez une définition d’entrée avec les [Annotations Kubernetes suivantes pour les sessions rémanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="4da95-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="4da95-154">Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="4da95-154">Linux with Nginx</span></span>

<span data-ttu-id="4da95-155">Pour SignalR que WebSocket fonctionne correctement, vérifiez que les en-têtes `Upgrade` et `Connection` du proxy sont définis sur les valeurs suivantes et qu' `$connection_upgrade` ils sont mappés à l’un ou l’autre des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="4da95-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="4da95-156">Valeur d’en-tête de mise à niveau par défaut.</span><span class="sxs-lookup"><span data-stu-id="4da95-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="4da95-157">`close`Lorsque l’en-tête de mise à niveau est manquant ou vide.</span><span class="sxs-lookup"><span data-stu-id="4da95-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="4da95-158">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="4da95-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="4da95-159">NGINX en tant que proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="4da95-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="4da95-160">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="4da95-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="4da95-161">Mesurer la latence du réseau</span><span class="sxs-lookup"><span data-stu-id="4da95-161">Measure network latency</span></span>

<span data-ttu-id="4da95-162">L' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) peut être utilisée pour mesurer la latence du réseau, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="4da95-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="4da95-163">Pour une expérience d’interface utilisateur raisonnable, nous recommandons une latence d’interface utilisateur soutenue de 250 ms ou moins.</span><span class="sxs-lookup"><span data-stu-id="4da95-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
