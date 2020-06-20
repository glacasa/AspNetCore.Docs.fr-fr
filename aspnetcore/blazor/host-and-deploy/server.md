---
title: Héberger et déployer ASP.NET Core Blazor serveur
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application serveur à l’aide de ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/server
ms.openlocfilehash: 891714250bc4e8b0b241e589efb268b8f8e9f0d5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103677"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="770f9-103">Héberger et déployer un Blazor serveur</span><span class="sxs-lookup"><span data-stu-id="770f9-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="770f9-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="770f9-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="770f9-105">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="770f9-105">Host configuration values</span></span>

<span data-ttu-id="770f9-106">Les [ Blazor applications serveur](xref:blazor/hosting-models#blazor-server) peuvent accepter des [valeurs de configuration d’hôte générique](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="770f9-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="770f9-107">Déploiement</span><span class="sxs-lookup"><span data-stu-id="770f9-107">Deployment</span></span>

<span data-ttu-id="770f9-108">À l’aide du [ Blazor modèle d’hébergement de serveur](xref:blazor/hosting-models#blazor-server), Blazor est exécuté sur le serveur à partir d’une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="770f9-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="770f9-109">Les mises à jour de l’interface utilisateur, la gestion des événements et les appels JavaScript sont gérés sur une [SignalR](xref:signalr/introduction) connexion.</span><span class="sxs-lookup"><span data-stu-id="770f9-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="770f9-110">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="770f9-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="770f9-111">Visual Studio comprend le modèle de projet d' \*\* Blazor application serveur\*\* ( `blazorserverside` modèle lors de l’utilisation de la commande [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="770f9-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="770f9-112">Extensibilité</span><span class="sxs-lookup"><span data-stu-id="770f9-112">Scalability</span></span>

<span data-ttu-id="770f9-113">Planifiez un déploiement pour tirer le meilleur parti de l’infrastructure disponible pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="770f9-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="770f9-114">Consultez les ressources suivantes pour résoudre l' Blazor extensibilité des applications serveur :</span><span class="sxs-lookup"><span data-stu-id="770f9-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="770f9-115">[Notions de base des Blazor applications serveur](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="770f9-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="770f9-116">Serveur de déploiement</span><span class="sxs-lookup"><span data-stu-id="770f9-116">Deployment server</span></span>

<span data-ttu-id="770f9-117">Lorsque vous envisagez l’évolutivité d’un serveur unique (montée en puissance), la mémoire disponible pour une application est probablement la première ressource que l’application épuisera en fonction des demandes des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="770f9-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="770f9-118">La mémoire disponible sur le serveur affecte les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="770f9-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="770f9-119">Nombre de circuits actifs qu’un serveur peut prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="770f9-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="770f9-120">Latence de l’interface utilisateur sur le client.</span><span class="sxs-lookup"><span data-stu-id="770f9-120">UI latency on the client.</span></span>

<span data-ttu-id="770f9-121">Pour obtenir des conseils sur la création d’applications serveur sécurisées et évolutives Blazor , consultez <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="770f9-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="770f9-122">Chaque circuit utilise environ 250 Ko de mémoire pour une application de type *Hello World*minimale.</span><span class="sxs-lookup"><span data-stu-id="770f9-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="770f9-123">La taille d’un circuit dépend du code de l’application et des exigences de maintenance d’état associées à chaque composant.</span><span class="sxs-lookup"><span data-stu-id="770f9-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="770f9-124">Nous vous recommandons de mesurer les demandes de ressources pendant le développement de votre application et de votre infrastructure, mais la ligne de base suivante peut être un point de départ pour la planification de votre cible de déploiement : Si vous pensez que votre application prend en charge 5 000 utilisateurs simultanés, envisagez de budgétiser au moins 1,3 Go de mémoire serveur vers l’application (ou ~ 273 Ko par utilisateur)</span><span class="sxs-lookup"><span data-stu-id="770f9-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="770f9-125">configuré</span><span class="sxs-lookup"><span data-stu-id="770f9-125"> configuration</span></span>

Blazor<span data-ttu-id="770f9-126">Les applications serveur utilisent ASP.NET Core SignalR pour communiquer avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="770f9-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="770f9-127">[ SignalR les conditions d’hébergement et de mise à l’échelle de](xref:signalr/publish-to-azure-web-app) s’appliquent à Blazor Applications serveur.</span><span class="sxs-lookup"><span data-stu-id="770f9-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="770f9-128">fonctionne mieux lorsque vous utilisez WebSocket en tant que SignalR transport en raison d’une latence, d’une fiabilité et d’une [sécurité](xref:signalr/security)moindres.</span><span class="sxs-lookup"><span data-stu-id="770f9-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="770f9-129">L’interrogation longue est utilisée par SignalR lorsque WebSocket n’est pas disponible ou lorsque l’application est configurée explicitement pour utiliser une interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="770f9-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="770f9-130">Lors du déploiement sur Azure App Service, configurez l’application pour qu’elle utilise WebSockets dans les paramètres Portail Azure pour le service.</span><span class="sxs-lookup"><span data-stu-id="770f9-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="770f9-131">Pour plus d’informations sur la configuration de l’application pour Azure App Service, consultez les [ SignalR instructions de publication](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="770f9-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="770f9-132">SignalRService Azure</span><span class="sxs-lookup"><span data-stu-id="770f9-132">Azure SignalR Service</span></span>

<span data-ttu-id="770f9-133">Nous vous recommandons d’utiliser le [ SignalR service Azure](/azure/azure-signalr) pour les Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="770f9-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="770f9-134">Le service permet la mise à l’échelle d’une Blazor application serveur vers un grand nombre de connexions simultanées SignalR .</span><span class="sxs-lookup"><span data-stu-id="770f9-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="770f9-135">En outre, la SignalR portée mondiale et les centres de données haute performance du service contribuent de manière significative à réduire la latence en raison de la géographie.</span><span class="sxs-lookup"><span data-stu-id="770f9-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="770f9-136">Pour configurer une application (et éventuellement approvisionner) le SignalR service Azure :</span><span class="sxs-lookup"><span data-stu-id="770f9-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="770f9-137">Activez le service pour prendre en charge les *sessions rémanentes*, où les clients sont [redirigés vers le même serveur lors du prérendu](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="770f9-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="770f9-138">Définissez l' `ServerStickyMode` option ou la valeur de configuration sur `Required` .</span><span class="sxs-lookup"><span data-stu-id="770f9-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="770f9-139">En règle générale, une application crée la configuration à l’aide de l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="770f9-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="770f9-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="770f9-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="770f9-141">Configuration (utilisez l' **une** des approches suivantes) :</span><span class="sxs-lookup"><span data-stu-id="770f9-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="770f9-142">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="770f9-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="770f9-143">Les paramètres d’application de **configuration**de l’app service  >  **Application settings** dans le portail Azure (**nom**: `Azure:SignalR:ServerStickyMode` , **valeur**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="770f9-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="770f9-144">Créez un profil de publication Azure Apps dans Visual Studio pour l' Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="770f9-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="770f9-145">Ajoutez la dépendance du \*\* SignalR service Azure\*\* au profil.</span><span class="sxs-lookup"><span data-stu-id="770f9-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="770f9-146">Si l’abonnement Azure n’a pas d’instance de service Azure préexistante SignalR à attribuer à l’application, sélectionnez **créer une nouvelle instance de SignalR service Azure** pour approvisionner une nouvelle instance de service.</span><span class="sxs-lookup"><span data-stu-id="770f9-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="770f9-147">Publiez l’application dans Azure.</span><span class="sxs-lookup"><span data-stu-id="770f9-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="770f9-148">IIS</span><span class="sxs-lookup"><span data-stu-id="770f9-148">IIS</span></span>

<span data-ttu-id="770f9-149">Lorsque vous utilisez IIS, activez :</span><span class="sxs-lookup"><span data-stu-id="770f9-149">When using IIS, enable:</span></span>

* <span data-ttu-id="770f9-150">[WebSocket sur IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="770f9-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="770f9-151">[Sessions rémanentes avec application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="770f9-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="770f9-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="770f9-152">Kubernetes</span></span>

<span data-ttu-id="770f9-153">Créez une définition d’entrée avec les [Annotations Kubernetes suivantes pour les sessions rémanentes](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="770f9-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="770f9-154">Linux avec Nginx</span><span class="sxs-lookup"><span data-stu-id="770f9-154">Linux with Nginx</span></span>

<span data-ttu-id="770f9-155">Pour que SignalR WebSocket fonctionne correctement, vérifiez que les `Upgrade` `Connection` en-têtes et du proxy sont définis sur les valeurs suivantes et qu’ils sont `$connection_upgrade` mappés à l’un ou l’autre des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="770f9-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="770f9-156">Valeur d’en-tête de mise à niveau par défaut.</span><span class="sxs-lookup"><span data-stu-id="770f9-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="770f9-157">`close`Lorsque l’en-tête de mise à niveau est manquant ou vide.</span><span class="sxs-lookup"><span data-stu-id="770f9-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="770f9-158">Pour plus d’informations, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="770f9-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="770f9-159">NGINX en tant que proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="770f9-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="770f9-160">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="770f9-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="770f9-161">Linux avec Apache</span><span class="sxs-lookup"><span data-stu-id="770f9-161">Linux with Apache</span></span>

<span data-ttu-id="770f9-162">Pour héberger une Blazor application derrière Apache sur Linux, configurez `ProxyPass` pour le trafic HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="770f9-162">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="770f9-163">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="770f9-163">In the following example:</span></span>

* <span data-ttu-id="770f9-164">Le serveur Kestrel est en cours d’exécution sur l’ordinateur hôte.</span><span class="sxs-lookup"><span data-stu-id="770f9-164">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="770f9-165">L’application écoute le trafic sur le port 5000.</span><span class="sxs-lookup"><span data-stu-id="770f9-165">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="770f9-166">Activez les modules suivants :</span><span class="sxs-lookup"><span data-stu-id="770f9-166">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="770f9-167">Consultez la console du navigateur pour les erreurs WebSocket.</span><span class="sxs-lookup"><span data-stu-id="770f9-167">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="770f9-168">Exemples d’erreurs :</span><span class="sxs-lookup"><span data-stu-id="770f9-168">Example errors:</span></span>

* <span data-ttu-id="770f9-169">Firefox ne peut pas établir de connexion au serveur sur ws://the-domain-name.tld/_blazor ?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="770f9-169">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="770f9-170">Erreur : échec du démarrage du transport’WebSockets' : erreur : une erreur s’est produite avec le transport.</span><span class="sxs-lookup"><span data-stu-id="770f9-170">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="770f9-171">Erreur : impossible de démarrer le transport’LongPolling' : TypeError : This. transport n’est pas défini</span><span class="sxs-lookup"><span data-stu-id="770f9-171">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="770f9-172">Erreur : impossible de se connecter au serveur avec l’un des transports disponibles.</span><span class="sxs-lookup"><span data-stu-id="770f9-172">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="770f9-173">Échec de WebSocket</span><span class="sxs-lookup"><span data-stu-id="770f9-173">WebSockets failed</span></span>
* <span data-ttu-id="770f9-174">Erreur : impossible d’envoyer des données si la connexion n’est pas dans l’état « connecté ».</span><span class="sxs-lookup"><span data-stu-id="770f9-174">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="770f9-175">Pour plus d’informations, consultez la [documentation Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="770f9-175">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="770f9-176">Mesurer la latence du réseau</span><span class="sxs-lookup"><span data-stu-id="770f9-176">Measure network latency</span></span>

<span data-ttu-id="770f9-177">L' [interopérabilité js](xref:blazor/call-javascript-from-dotnet) peut être utilisée pour mesurer la latence du réseau, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="770f9-177">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="770f9-178">Pour une expérience d’interface utilisateur raisonnable, nous recommandons une latence d’interface utilisateur soutenue de 250 ms ou moins.</span><span class="sxs-lookup"><span data-stu-id="770f9-178">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>