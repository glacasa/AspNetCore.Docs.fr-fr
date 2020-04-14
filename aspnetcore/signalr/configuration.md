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
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228138"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9f216-103">Configuration d’ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9f216-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9f216-104">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9f216-105">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9f216-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9f216-106">Chaque protocole a des options de configuration de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="9f216-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9f216-107">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode [d’extension AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="9f216-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9f216-108">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f216-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f216-109">La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet.</span><span class="sxs-lookup"><span data-stu-id="9f216-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9f216-110">La [propriété PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> cet objet est un objet qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="9f216-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9f216-111">Pour plus d’informations, voir la [documentation System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9f216-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9f216-112">Par exemple, pour configurer le sérialisateur pour ne pas modifier le boîtier des noms de `Startup.ConfigureServices`propriété, au lieu des noms par défaut "camelCase", utilisez le code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="9f216-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9f216-113">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9f216-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9f216-114">L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9f216-115">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9f216-116">Passez à Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="9f216-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9f216-117">Si vous avez `Newtonsoft.Json` besoin de fonctionnalités de ce qui ne sont pas pris en charge , `System.Text.Json`Voir Passer à [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9f216-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9f216-118">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-118">MessagePack serialization options</span></span>

<span data-ttu-id="9f216-119">La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="9f216-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9f216-120">Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="9f216-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-121">Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9f216-122">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="9f216-122">Configure server options</span></span>

<span data-ttu-id="9f216-123">Le tableau suivant décrit les options de configuration des moyeux SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9f216-124">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-124">Option</span></span> | <span data-ttu-id="9f216-125">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-125">Default Value</span></span> | <span data-ttu-id="9f216-126">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9f216-127">30 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-127">30 seconds</span></span> | <span data-ttu-id="9f216-128">Le serveur considérera le client déconnecté s’il n’a pas reçu de message (y compris le maintien en vie) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9f216-129">Il pourrait prendre plus de temps que cet intervalle de délai d’attente pour le client d’être effectivement marqué déconnecté, en raison de la façon dont cela est mis en œuvre.</span><span class="sxs-lookup"><span data-stu-id="9f216-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9f216-130">La valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9f216-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9f216-131">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-131">15 seconds</span></span> | <span data-ttu-id="9f216-132">Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="9f216-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9f216-133">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-134">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9f216-135">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-135">15 seconds</span></span> | <span data-ttu-id="9f216-136">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="9f216-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9f216-137">Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="9f216-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9f216-138">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9f216-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9f216-139">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="9f216-139">All installed protocols</span></span> | <span data-ttu-id="9f216-140">Protocoles soutenus par ce hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9f216-141">Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="9f216-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9f216-142">Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9f216-143">La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="9f216-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9f216-144">Le nombre maximum d’éléments qui peuvent être tamponnés pour les flux de téléchargement des clients.</span><span class="sxs-lookup"><span data-stu-id="9f216-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9f216-145">Si cette limite est atteinte, le traitement des invocations est bloqué jusqu’à ce que le serveur traite les éléments du flux.</span><span class="sxs-lookup"><span data-stu-id="9f216-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9f216-146">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-146">32 KB</span></span> | <span data-ttu-id="9f216-147">Taille maximale d’un seul message de hub entrant.</span><span class="sxs-lookup"><span data-stu-id="9f216-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9f216-148">Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .</span><span class="sxs-lookup"><span data-stu-id="9f216-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9f216-149">Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="9f216-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9f216-150">Options avancées de configuration HTTP</span><span class="sxs-lookup"><span data-stu-id="9f216-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9f216-151">Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9f216-152">Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans .</span><span class="sxs-lookup"><span data-stu-id="9f216-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9f216-153">Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9f216-154">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-154">Option</span></span> | <span data-ttu-id="9f216-155">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-155">Default Value</span></span> | <span data-ttu-id="9f216-156">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9f216-157">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-157">32 KB</span></span> | <span data-ttu-id="9f216-158">Le nombre maximum d’octets reçus du client que le serveur tampons avant d’appliquer backpressure.</span><span class="sxs-lookup"><span data-stu-id="9f216-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9f216-159">L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands plus rapidement sans appliquer la rétropression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9f216-160">Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9f216-161">Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9f216-162">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-162">32 KB</span></span> | <span data-ttu-id="9f216-163">Le nombre maximum d’octets envoyés par l’application que le serveur tampons avant d’observer la rétropression.</span><span class="sxs-lookup"><span data-stu-id="9f216-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9f216-164">L’augmentation de cette valeur permet au serveur de tamponner les messages plus grands plus rapidement sans attendre backpressure, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9f216-165">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="9f216-165">All Transports are enabled.</span></span> | <span data-ttu-id="9f216-166">Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="9f216-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9f216-167">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-167">See below.</span></span> | <span data-ttu-id="9f216-168">Options supplémentaires spécifiques au transport à long scrutin.</span><span class="sxs-lookup"><span data-stu-id="9f216-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9f216-169">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-169">See below.</span></span> | <span data-ttu-id="9f216-170">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9f216-171">0</span><span class="sxs-lookup"><span data-stu-id="9f216-171">0</span></span> | <span data-ttu-id="9f216-172">Spécifier la version minimale du protocole de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9f216-173">Ceci est utilisé pour limiter les clients à de nouvelles versions.</span><span class="sxs-lookup"><span data-stu-id="9f216-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9f216-174">Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9f216-175">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-175">Option</span></span> | <span data-ttu-id="9f216-176">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-176">Default Value</span></span> | <span data-ttu-id="9f216-177">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9f216-178">90 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-178">90 seconds</span></span> | <span data-ttu-id="9f216-179">Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9f216-180">La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9f216-181">Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9f216-182">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-182">Option</span></span> | <span data-ttu-id="9f216-183">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-183">Default Value</span></span> | <span data-ttu-id="9f216-184">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9f216-185">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-185">5 seconds</span></span> | <span data-ttu-id="9f216-186">Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="9f216-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9f216-187">Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="9f216-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9f216-188">Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="9f216-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9f216-189">Configurer les options client</span><span class="sxs-lookup"><span data-stu-id="9f216-189">Configure client options</span></span>

<span data-ttu-id="9f216-190">Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9f216-191">Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="9f216-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9f216-192">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="9f216-192">Configure logging</span></span>

<span data-ttu-id="9f216-193">L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9f216-194">Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9f216-195">Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="9f216-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-196">Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires.</span><span class="sxs-lookup"><span data-stu-id="9f216-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9f216-197">Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.</span><span class="sxs-lookup"><span data-stu-id="9f216-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9f216-198">Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="9f216-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9f216-199">Appelez `AddConsole` la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9f216-200">Dans le client JavaScript, une méthode similaire `configureLogging` existe.</span><span class="sxs-lookup"><span data-stu-id="9f216-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9f216-201">Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire.</span><span class="sxs-lookup"><span data-stu-id="9f216-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9f216-202">Les journaux sont écrits sur la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f216-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9f216-203">Au lieu `LogLevel` d’une valeur, `string` vous pouvez également fournir une valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="9f216-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9f216-204">Ceci est utile lors de la configuration de l’enregistrement SignalR `LogLevel` dans des environnements où vous n’avez pas accès aux constantes.</span><span class="sxs-lookup"><span data-stu-id="9f216-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9f216-205">Le tableau suivant répertorie les niveaux de journal disponibles.</span><span class="sxs-lookup"><span data-stu-id="9f216-205">The following table lists the available log levels.</span></span> <span data-ttu-id="9f216-206">La valeur que `configureLogging` vous fournissez pour établir le niveau **minimum** de journal qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="9f216-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9f216-207">Les messages enregistrés à ce niveau, **ou les niveaux énumérés après lui dans le tableau,** seront enregistrés.</span><span class="sxs-lookup"><span data-stu-id="9f216-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9f216-208">String</span><span class="sxs-lookup"><span data-stu-id="9f216-208">String</span></span>                      | <span data-ttu-id="9f216-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9f216-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9f216-210">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="9f216-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9f216-211">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="9f216-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9f216-212">Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9f216-213">Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9f216-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9f216-214">Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="9f216-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9f216-215">Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière.</span><span class="sxs-lookup"><span data-stu-id="9f216-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9f216-216">L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9f216-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9f216-217">Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="9f216-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9f216-218">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="9f216-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9f216-219">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="9f216-219">Configure allowed transports</span></span>

<span data-ttu-id="9f216-220">Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9f216-221">Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="9f216-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9f216-222">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f216-222">All transports are enabled by default.</span></span>

<span data-ttu-id="9f216-223">Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :</span><span class="sxs-lookup"><span data-stu-id="9f216-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9f216-224">Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9f216-225">Dans cette version des websockets clients Java est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="9f216-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9f216-226">Dans le client Java, le `withTransport` transport est `HttpHubConnectionBuilder`sélectionné avec la méthode sur le .</span><span class="sxs-lookup"><span data-stu-id="9f216-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9f216-227">Le client Java par défaut à l’utilisation du transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9f216-228">Le client SignalR Java ne prend pas encore en charge le repli des transports.</span><span class="sxs-lookup"><span data-stu-id="9f216-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9f216-229">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="9f216-229">Configure bearer authentication</span></span>

<span data-ttu-id="9f216-230">Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="9f216-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9f216-231">Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ).</span><span class="sxs-lookup"><span data-stu-id="9f216-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9f216-232">Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web).</span><span class="sxs-lookup"><span data-stu-id="9f216-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9f216-233">Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .</span><span class="sxs-lookup"><span data-stu-id="9f216-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9f216-234">Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :</span><span class="sxs-lookup"><span data-stu-id="9f216-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9f216-235">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9f216-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9f216-236">Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9f216-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9f216-237">Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="9f216-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9f216-238">Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="9f216-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9f216-239">Configurer le délai d’attente et les options de maintien en vie</span><span class="sxs-lookup"><span data-stu-id="9f216-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9f216-240">D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:</span><span class="sxs-lookup"><span data-stu-id="9f216-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-241">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-242">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-242">Option</span></span> | <span data-ttu-id="9f216-243">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-243">Default value</span></span> | <span data-ttu-id="9f216-244">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9f216-245">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-246">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-246">Timeout for server activity.</span></span> <span data-ttu-id="9f216-247">Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-248">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-249">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9f216-250">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-250">15 seconds</span></span> | <span data-ttu-id="9f216-251">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-252">Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-253">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-254">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9f216-255">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-255">15 seconds</span></span> | <span data-ttu-id="9f216-256">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-257">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-258">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9f216-259">Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.</span><span class="sxs-lookup"><span data-stu-id="9f216-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9f216-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-261">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-261">Option</span></span> | <span data-ttu-id="9f216-262">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-262">Default value</span></span> | <span data-ttu-id="9f216-263">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9f216-264">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-265">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-265">Timeout for server activity.</span></span> <span data-ttu-id="9f216-266">Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9f216-267">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-268">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9f216-269">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9f216-270">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-271">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-272">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-273">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-273">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-274">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-274">Option</span></span> | <span data-ttu-id="9f216-275">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-275">Default value</span></span> | <span data-ttu-id="9f216-276">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9f216-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9f216-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9f216-278">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-279">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-279">Timeout for server activity.</span></span> <span data-ttu-id="9f216-280">Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-281">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-282">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9f216-283">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-283">15 seconds</span></span> | <span data-ttu-id="9f216-284">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-285">Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-286">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-287">Pour plus de détails sur le processus de poignée de main, consultez la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9f216-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9f216-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9f216-289">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9f216-290">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-291">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-292">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9f216-293">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-293">Configure additional options</span></span>

<span data-ttu-id="9f216-294">D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :</span><span class="sxs-lookup"><span data-stu-id="9f216-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-295">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-296">Option .NET</span><span class="sxs-lookup"><span data-stu-id="9f216-296">.NET Option</span></span> |  <span data-ttu-id="9f216-297">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-297">Default value</span></span> | <span data-ttu-id="9f216-298">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9f216-299">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9f216-300">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-301">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-302">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9f216-303">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-303">Empty</span></span> | <span data-ttu-id="9f216-304">Une collection de certificats TLS à envoyer pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="9f216-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9f216-305">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-305">Empty</span></span> | <span data-ttu-id="9f216-306">Une collection de cookies HTTP à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9f216-307">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-307">Empty</span></span> | <span data-ttu-id="9f216-308">Informations d’identification à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9f216-309">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-309">5 seconds</span></span> | <span data-ttu-id="9f216-310">WebSockets seulement.</span><span class="sxs-lookup"><span data-stu-id="9f216-310">WebSockets only.</span></span> <span data-ttu-id="9f216-311">Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche.</span><span class="sxs-lookup"><span data-stu-id="9f216-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9f216-312">Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="9f216-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9f216-313">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-313">Empty</span></span> | <span data-ttu-id="9f216-314">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9f216-315">Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9f216-316">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="9f216-317">Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f216-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9f216-318">Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="9f216-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9f216-319">**Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="9f216-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9f216-320">Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9f216-321">Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9f216-322">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="9f216-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9f216-323">Un délégué qui peut être utilisé pour configurer d’autres options WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9f216-324">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="9f216-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9f216-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-326">JavaScript Option</span><span class="sxs-lookup"><span data-stu-id="9f216-326">JavaScript Option</span></span> | <span data-ttu-id="9f216-327">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-327">Default Value</span></span> | <span data-ttu-id="9f216-328">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9f216-329">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9f216-330">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-331">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-332">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-333">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-333">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-334">Java Option</span><span class="sxs-lookup"><span data-stu-id="9f216-334">Java Option</span></span> | <span data-ttu-id="9f216-335">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-335">Default Value</span></span> | <span data-ttu-id="9f216-336">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9f216-337">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9f216-338">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-339">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-340">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9f216-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9f216-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9f216-342">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-342">Empty</span></span> | <span data-ttu-id="9f216-343">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9f216-344">Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :</span><span class="sxs-lookup"><span data-stu-id="9f216-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9f216-345">Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9f216-346">Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9f216-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9f216-347">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9f216-348">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9f216-349">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9f216-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9f216-350">Chaque protocole a des options de configuration de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="9f216-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9f216-351">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode [d’extension AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="9f216-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9f216-352">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f216-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f216-353">La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet.</span><span class="sxs-lookup"><span data-stu-id="9f216-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9f216-354">La [propriété PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> cet objet est un objet qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="9f216-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9f216-355">Pour plus d’informations, voir la [documentation System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9f216-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9f216-356">Par exemple, pour configurer le sérialisateur pour ne pas modifier le boîtier des noms de `Startup.ConfigureServices`propriété, au lieu des noms par défaut "camelCase", utilisez le code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="9f216-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9f216-357">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9f216-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9f216-358">L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9f216-359">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9f216-360">Passez à Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="9f216-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9f216-361">Si vous avez `Newtonsoft.Json` besoin de fonctionnalités de ce qui ne sont pas pris en charge , `System.Text.Json`Voir Passer à [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9f216-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9f216-362">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-362">MessagePack serialization options</span></span>

<span data-ttu-id="9f216-363">La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="9f216-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9f216-364">Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="9f216-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-365">Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9f216-366">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="9f216-366">Configure server options</span></span>

<span data-ttu-id="9f216-367">Le tableau suivant décrit les options de configuration des moyeux SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9f216-368">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-368">Option</span></span> | <span data-ttu-id="9f216-369">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-369">Default Value</span></span> | <span data-ttu-id="9f216-370">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9f216-371">30 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-371">30 seconds</span></span> | <span data-ttu-id="9f216-372">Le serveur considérera le client déconnecté s’il n’a pas reçu de message (y compris le maintien en vie) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9f216-373">Il pourrait prendre plus de temps que cet intervalle de délai d’attente pour le client d’être effectivement marqué déconnecté, en raison de la façon dont cela est mis en œuvre.</span><span class="sxs-lookup"><span data-stu-id="9f216-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9f216-374">La valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9f216-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9f216-375">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-375">15 seconds</span></span> | <span data-ttu-id="9f216-376">Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="9f216-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9f216-377">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-378">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9f216-379">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-379">15 seconds</span></span> | <span data-ttu-id="9f216-380">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="9f216-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9f216-381">Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="9f216-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9f216-382">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9f216-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9f216-383">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="9f216-383">All installed protocols</span></span> | <span data-ttu-id="9f216-384">Protocoles soutenus par ce hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-384">Protocols supported by this hub.</span></span> <span data-ttu-id="9f216-385">Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="9f216-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9f216-386">Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9f216-387">La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="9f216-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9f216-388">Le nombre maximum d’éléments qui peuvent être tamponnés pour les flux de téléchargement des clients.</span><span class="sxs-lookup"><span data-stu-id="9f216-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9f216-389">Si cette limite est atteinte, le traitement des invocations est bloqué jusqu’à ce que le serveur traite les éléments du flux.</span><span class="sxs-lookup"><span data-stu-id="9f216-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9f216-390">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-390">32 KB</span></span> | <span data-ttu-id="9f216-391">Taille maximale d’un seul message de hub entrant.</span><span class="sxs-lookup"><span data-stu-id="9f216-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9f216-392">Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .</span><span class="sxs-lookup"><span data-stu-id="9f216-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9f216-393">Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="9f216-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9f216-394">Options avancées de configuration HTTP</span><span class="sxs-lookup"><span data-stu-id="9f216-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9f216-395">Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9f216-396">Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans .</span><span class="sxs-lookup"><span data-stu-id="9f216-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9f216-397">Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9f216-398">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-398">Option</span></span> | <span data-ttu-id="9f216-399">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-399">Default Value</span></span> | <span data-ttu-id="9f216-400">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9f216-401">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-401">32 KB</span></span> | <span data-ttu-id="9f216-402">Le nombre maximum d’octets reçus du client que le serveur tampons avant d’appliquer backpressure.</span><span class="sxs-lookup"><span data-stu-id="9f216-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9f216-403">L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands plus rapidement sans appliquer la rétropression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9f216-404">Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9f216-405">Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9f216-406">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-406">32 KB</span></span> | <span data-ttu-id="9f216-407">Le nombre maximum d’octets envoyés par l’application que le serveur tampons avant d’observer la rétropression.</span><span class="sxs-lookup"><span data-stu-id="9f216-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9f216-408">L’augmentation de cette valeur permet au serveur de tamponner les messages plus grands plus rapidement sans attendre backpressure, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9f216-409">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="9f216-409">All Transports are enabled.</span></span> | <span data-ttu-id="9f216-410">Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="9f216-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9f216-411">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-411">See below.</span></span> | <span data-ttu-id="9f216-412">Options supplémentaires spécifiques au transport à long scrutin.</span><span class="sxs-lookup"><span data-stu-id="9f216-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9f216-413">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-413">See below.</span></span> | <span data-ttu-id="9f216-414">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9f216-415">Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9f216-416">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-416">Option</span></span> | <span data-ttu-id="9f216-417">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-417">Default Value</span></span> | <span data-ttu-id="9f216-418">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9f216-419">90 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-419">90 seconds</span></span> | <span data-ttu-id="9f216-420">Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9f216-421">La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9f216-422">Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9f216-423">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-423">Option</span></span> | <span data-ttu-id="9f216-424">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-424">Default Value</span></span> | <span data-ttu-id="9f216-425">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9f216-426">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-426">5 seconds</span></span> | <span data-ttu-id="9f216-427">Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="9f216-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9f216-428">Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="9f216-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9f216-429">Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="9f216-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9f216-430">Configurer les options client</span><span class="sxs-lookup"><span data-stu-id="9f216-430">Configure client options</span></span>

<span data-ttu-id="9f216-431">Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9f216-432">Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="9f216-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9f216-433">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="9f216-433">Configure logging</span></span>

<span data-ttu-id="9f216-434">L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9f216-435">Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9f216-436">Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="9f216-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-437">Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires.</span><span class="sxs-lookup"><span data-stu-id="9f216-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9f216-438">Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.</span><span class="sxs-lookup"><span data-stu-id="9f216-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9f216-439">Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="9f216-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9f216-440">Appelez `AddConsole` la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9f216-441">Dans le client JavaScript, une méthode similaire `configureLogging` existe.</span><span class="sxs-lookup"><span data-stu-id="9f216-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9f216-442">Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire.</span><span class="sxs-lookup"><span data-stu-id="9f216-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9f216-443">Les journaux sont écrits sur la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f216-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9f216-444">Au lieu `LogLevel` d’une valeur, `string` vous pouvez également fournir une valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="9f216-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9f216-445">Ceci est utile lors de la configuration de l’enregistrement SignalR `LogLevel` dans des environnements où vous n’avez pas accès aux constantes.</span><span class="sxs-lookup"><span data-stu-id="9f216-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9f216-446">Le tableau suivant répertorie les niveaux de journal disponibles.</span><span class="sxs-lookup"><span data-stu-id="9f216-446">The following table lists the available log levels.</span></span> <span data-ttu-id="9f216-447">La valeur que `configureLogging` vous fournissez pour établir le niveau **minimum** de journal qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="9f216-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9f216-448">Les messages enregistrés à ce niveau, **ou les niveaux énumérés après lui dans le tableau,** seront enregistrés.</span><span class="sxs-lookup"><span data-stu-id="9f216-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9f216-449">String</span><span class="sxs-lookup"><span data-stu-id="9f216-449">String</span></span>                      | <span data-ttu-id="9f216-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9f216-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9f216-451">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="9f216-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9f216-452">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="9f216-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9f216-453">Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9f216-454">Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9f216-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9f216-455">Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="9f216-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9f216-456">Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière.</span><span class="sxs-lookup"><span data-stu-id="9f216-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9f216-457">L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9f216-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9f216-458">Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="9f216-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9f216-459">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="9f216-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9f216-460">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="9f216-460">Configure allowed transports</span></span>

<span data-ttu-id="9f216-461">Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9f216-462">Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="9f216-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9f216-463">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f216-463">All transports are enabled by default.</span></span>

<span data-ttu-id="9f216-464">Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :</span><span class="sxs-lookup"><span data-stu-id="9f216-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9f216-465">Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9f216-466">Dans cette version des websockets clients Java est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="9f216-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9f216-467">Dans le client Java, le `withTransport` transport est `HttpHubConnectionBuilder`sélectionné avec la méthode sur le .</span><span class="sxs-lookup"><span data-stu-id="9f216-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9f216-468">Le client Java par défaut à l’utilisation du transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9f216-469">Le client SignalR Java ne prend pas encore en charge le repli des transports.</span><span class="sxs-lookup"><span data-stu-id="9f216-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9f216-470">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="9f216-470">Configure bearer authentication</span></span>

<span data-ttu-id="9f216-471">Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="9f216-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9f216-472">Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ).</span><span class="sxs-lookup"><span data-stu-id="9f216-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9f216-473">Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web).</span><span class="sxs-lookup"><span data-stu-id="9f216-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9f216-474">Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .</span><span class="sxs-lookup"><span data-stu-id="9f216-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9f216-475">Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :</span><span class="sxs-lookup"><span data-stu-id="9f216-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9f216-476">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9f216-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9f216-477">Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9f216-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9f216-478">Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="9f216-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9f216-479">Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="9f216-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9f216-480">Configurer le délai d’attente et les options de maintien en vie</span><span class="sxs-lookup"><span data-stu-id="9f216-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9f216-481">D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:</span><span class="sxs-lookup"><span data-stu-id="9f216-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-482">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-483">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-483">Option</span></span> | <span data-ttu-id="9f216-484">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-484">Default value</span></span> | <span data-ttu-id="9f216-485">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9f216-486">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-487">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-487">Timeout for server activity.</span></span> <span data-ttu-id="9f216-488">Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-489">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-490">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9f216-491">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-491">15 seconds</span></span> | <span data-ttu-id="9f216-492">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-493">Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-494">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-495">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9f216-496">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-496">15 seconds</span></span> | <span data-ttu-id="9f216-497">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-498">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-499">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9f216-500">Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.</span><span class="sxs-lookup"><span data-stu-id="9f216-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9f216-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-502">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-502">Option</span></span> | <span data-ttu-id="9f216-503">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-503">Default value</span></span> | <span data-ttu-id="9f216-504">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9f216-505">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-506">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-506">Timeout for server activity.</span></span> <span data-ttu-id="9f216-507">Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9f216-508">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-509">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9f216-510">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9f216-511">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-512">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-513">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-514">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-514">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-515">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-515">Option</span></span> | <span data-ttu-id="9f216-516">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-516">Default value</span></span> | <span data-ttu-id="9f216-517">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9f216-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9f216-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9f216-519">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-520">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-520">Timeout for server activity.</span></span> <span data-ttu-id="9f216-521">Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-522">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-523">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9f216-524">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-524">15 seconds</span></span> | <span data-ttu-id="9f216-525">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-526">Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-527">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-528">Pour plus de détails sur le processus de poignée de main, consultez la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9f216-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9f216-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9f216-530">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9f216-531">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-532">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-533">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9f216-534">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-534">Configure additional options</span></span>

<span data-ttu-id="9f216-535">D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :</span><span class="sxs-lookup"><span data-stu-id="9f216-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-536">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-537">Option .NET</span><span class="sxs-lookup"><span data-stu-id="9f216-537">.NET Option</span></span> |  <span data-ttu-id="9f216-538">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-538">Default value</span></span> | <span data-ttu-id="9f216-539">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9f216-540">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9f216-541">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-542">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-543">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9f216-544">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-544">Empty</span></span> | <span data-ttu-id="9f216-545">Une collection de certificats TLS à envoyer pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="9f216-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9f216-546">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-546">Empty</span></span> | <span data-ttu-id="9f216-547">Une collection de cookies HTTP à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9f216-548">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-548">Empty</span></span> | <span data-ttu-id="9f216-549">Informations d’identification à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9f216-550">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-550">5 seconds</span></span> | <span data-ttu-id="9f216-551">WebSockets seulement.</span><span class="sxs-lookup"><span data-stu-id="9f216-551">WebSockets only.</span></span> <span data-ttu-id="9f216-552">Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche.</span><span class="sxs-lookup"><span data-stu-id="9f216-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9f216-553">Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="9f216-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9f216-554">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-554">Empty</span></span> | <span data-ttu-id="9f216-555">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9f216-556">Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9f216-557">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="9f216-558">Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f216-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9f216-559">Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="9f216-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9f216-560">**Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="9f216-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9f216-561">Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9f216-562">Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9f216-563">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="9f216-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9f216-564">Un délégué qui peut être utilisé pour configurer d’autres options WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9f216-565">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="9f216-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9f216-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-567">JavaScript Option</span><span class="sxs-lookup"><span data-stu-id="9f216-567">JavaScript Option</span></span> | <span data-ttu-id="9f216-568">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-568">Default Value</span></span> | <span data-ttu-id="9f216-569">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9f216-570">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9f216-571">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-572">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-573">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-574">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-574">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-575">Java Option</span><span class="sxs-lookup"><span data-stu-id="9f216-575">Java Option</span></span> | <span data-ttu-id="9f216-576">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-576">Default Value</span></span> | <span data-ttu-id="9f216-577">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9f216-578">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9f216-579">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-580">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-581">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9f216-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9f216-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9f216-583">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-583">Empty</span></span> | <span data-ttu-id="9f216-584">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9f216-585">Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :</span><span class="sxs-lookup"><span data-stu-id="9f216-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9f216-586">Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9f216-587">Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9f216-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9f216-588">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9f216-589">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9f216-590">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9f216-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9f216-591">Chaque protocole a des options de configuration de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="9f216-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9f216-592">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode `Startup.ConfigureServices` [d’extension AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9f216-593">La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet.</span><span class="sxs-lookup"><span data-stu-id="9f216-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9f216-594">La [propriété PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet `JsonSerializerSettings` est un objet JSON.NET qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="9f216-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9f216-595">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9f216-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9f216-596">À titre d’exemple, pour configurer le sérialisateur pour utiliser les noms de propriété "PascalCase", au lieu des noms par défaut "camelCase", utilisez le code suivant dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f216-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9f216-597">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9f216-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9f216-598">L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9f216-599">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9f216-600">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-600">MessagePack serialization options</span></span>

<span data-ttu-id="9f216-601">La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="9f216-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9f216-602">Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="9f216-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-603">Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9f216-604">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="9f216-604">Configure server options</span></span>

<span data-ttu-id="9f216-605">Le tableau suivant décrit les options de configuration des moyeux SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9f216-606">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-606">Option</span></span> | <span data-ttu-id="9f216-607">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-607">Default Value</span></span> | <span data-ttu-id="9f216-608">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9f216-609">30 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-609">30 seconds</span></span> | <span data-ttu-id="9f216-610">Le serveur considérera le client déconnecté s’il n’a pas reçu de message (y compris le maintien en vie) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9f216-611">Il pourrait prendre plus de temps que cet intervalle de délai d’attente pour le client d’être effectivement marqué déconnecté, en raison de la façon dont cela est mis en œuvre.</span><span class="sxs-lookup"><span data-stu-id="9f216-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9f216-612">La valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9f216-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9f216-613">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-613">15 seconds</span></span> | <span data-ttu-id="9f216-614">Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="9f216-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9f216-615">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-616">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9f216-617">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-617">15 seconds</span></span> | <span data-ttu-id="9f216-618">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="9f216-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9f216-619">Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="9f216-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9f216-620">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9f216-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9f216-621">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="9f216-621">All installed protocols</span></span> | <span data-ttu-id="9f216-622">Protocoles soutenus par ce hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-622">Protocols supported by this hub.</span></span> <span data-ttu-id="9f216-623">Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="9f216-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9f216-624">Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9f216-625">La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="9f216-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9f216-626">Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .</span><span class="sxs-lookup"><span data-stu-id="9f216-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9f216-627">Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="9f216-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9f216-628">Options avancées de configuration HTTP</span><span class="sxs-lookup"><span data-stu-id="9f216-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9f216-629">Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9f216-630">Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans .</span><span class="sxs-lookup"><span data-stu-id="9f216-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9f216-631">Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9f216-632">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-632">Option</span></span> | <span data-ttu-id="9f216-633">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-633">Default Value</span></span> | <span data-ttu-id="9f216-634">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9f216-635">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-635">32 KB</span></span> | <span data-ttu-id="9f216-636">Le nombre maximum d’octets reçus du client que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="9f216-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9f216-637">L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9f216-638">Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9f216-639">Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9f216-640">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-640">32 KB</span></span> | <span data-ttu-id="9f216-641">Le nombre maximum d’octets envoyés par l’application que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="9f216-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9f216-642">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9f216-643">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="9f216-643">All Transports are enabled.</span></span> | <span data-ttu-id="9f216-644">Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="9f216-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9f216-645">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-645">See below.</span></span> | <span data-ttu-id="9f216-646">Options supplémentaires spécifiques au transport à long scrutin.</span><span class="sxs-lookup"><span data-stu-id="9f216-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9f216-647">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-647">See below.</span></span> | <span data-ttu-id="9f216-648">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9f216-649">Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9f216-650">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-650">Option</span></span> | <span data-ttu-id="9f216-651">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-651">Default Value</span></span> | <span data-ttu-id="9f216-652">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9f216-653">90 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-653">90 seconds</span></span> | <span data-ttu-id="9f216-654">Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9f216-655">La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9f216-656">Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9f216-657">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-657">Option</span></span> | <span data-ttu-id="9f216-658">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-658">Default Value</span></span> | <span data-ttu-id="9f216-659">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9f216-660">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-660">5 seconds</span></span> | <span data-ttu-id="9f216-661">Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="9f216-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9f216-662">Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="9f216-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9f216-663">Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="9f216-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9f216-664">Configurer les options client</span><span class="sxs-lookup"><span data-stu-id="9f216-664">Configure client options</span></span>

<span data-ttu-id="9f216-665">Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9f216-666">Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="9f216-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9f216-667">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="9f216-667">Configure logging</span></span>

<span data-ttu-id="9f216-668">L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9f216-669">Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9f216-670">Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="9f216-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-671">Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires.</span><span class="sxs-lookup"><span data-stu-id="9f216-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9f216-672">Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.</span><span class="sxs-lookup"><span data-stu-id="9f216-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9f216-673">Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="9f216-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9f216-674">Appelez `AddConsole` la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9f216-675">Dans le client JavaScript, une méthode similaire `configureLogging` existe.</span><span class="sxs-lookup"><span data-stu-id="9f216-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9f216-676">Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire.</span><span class="sxs-lookup"><span data-stu-id="9f216-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9f216-677">Les journaux sont écrits sur la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f216-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9f216-678">Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9f216-679">Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9f216-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9f216-680">Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="9f216-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9f216-681">Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière.</span><span class="sxs-lookup"><span data-stu-id="9f216-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9f216-682">L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9f216-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9f216-683">Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="9f216-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9f216-684">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="9f216-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9f216-685">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="9f216-685">Configure allowed transports</span></span>

<span data-ttu-id="9f216-686">Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9f216-687">Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="9f216-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9f216-688">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f216-688">All transports are enabled by default.</span></span>

<span data-ttu-id="9f216-689">Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :</span><span class="sxs-lookup"><span data-stu-id="9f216-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9f216-690">Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9f216-691">Dans cette version des websockets clients Java est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="9f216-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9f216-692">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="9f216-692">Configure bearer authentication</span></span>

<span data-ttu-id="9f216-693">Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="9f216-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9f216-694">Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ).</span><span class="sxs-lookup"><span data-stu-id="9f216-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9f216-695">Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web).</span><span class="sxs-lookup"><span data-stu-id="9f216-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9f216-696">Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .</span><span class="sxs-lookup"><span data-stu-id="9f216-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9f216-697">Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :</span><span class="sxs-lookup"><span data-stu-id="9f216-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9f216-698">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9f216-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9f216-699">Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9f216-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9f216-700">Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="9f216-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9f216-701">Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="9f216-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9f216-702">Configurer le délai d’attente et les options de maintien en vie</span><span class="sxs-lookup"><span data-stu-id="9f216-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9f216-703">D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:</span><span class="sxs-lookup"><span data-stu-id="9f216-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-704">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-705">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-705">Option</span></span> | <span data-ttu-id="9f216-706">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-706">Default value</span></span> | <span data-ttu-id="9f216-707">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9f216-708">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-709">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-709">Timeout for server activity.</span></span> <span data-ttu-id="9f216-710">Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-711">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-712">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9f216-713">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-713">15 seconds</span></span> | <span data-ttu-id="9f216-714">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-715">Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-716">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-717">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9f216-718">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-718">15 seconds</span></span> | <span data-ttu-id="9f216-719">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-720">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-721">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9f216-722">Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.</span><span class="sxs-lookup"><span data-stu-id="9f216-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9f216-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-724">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-724">Option</span></span> | <span data-ttu-id="9f216-725">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-725">Default value</span></span> | <span data-ttu-id="9f216-726">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9f216-727">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-728">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-728">Timeout for server activity.</span></span> <span data-ttu-id="9f216-729">Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9f216-730">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-731">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9f216-732">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9f216-733">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-734">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-735">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-736">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-736">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-737">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-737">Option</span></span> | <span data-ttu-id="9f216-738">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-738">Default value</span></span> | <span data-ttu-id="9f216-739">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9f216-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9f216-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9f216-741">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-742">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-742">Timeout for server activity.</span></span> <span data-ttu-id="9f216-743">Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-744">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-745">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9f216-746">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-746">15 seconds</span></span> | <span data-ttu-id="9f216-747">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-748">Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-749">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-750">Pour plus de détails sur le processus de poignée de main, consultez la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9f216-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9f216-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9f216-752">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9f216-753">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="9f216-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9f216-754">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="9f216-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9f216-755">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="9f216-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9f216-756">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-756">Configure additional options</span></span>

<span data-ttu-id="9f216-757">D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :</span><span class="sxs-lookup"><span data-stu-id="9f216-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-758">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-759">Option .NET</span><span class="sxs-lookup"><span data-stu-id="9f216-759">.NET Option</span></span> |  <span data-ttu-id="9f216-760">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-760">Default value</span></span> | <span data-ttu-id="9f216-761">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9f216-762">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9f216-763">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-764">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-765">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9f216-766">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-766">Empty</span></span> | <span data-ttu-id="9f216-767">Une collection de certificats TLS à envoyer pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="9f216-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9f216-768">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-768">Empty</span></span> | <span data-ttu-id="9f216-769">Une collection de cookies HTTP à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9f216-770">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-770">Empty</span></span> | <span data-ttu-id="9f216-771">Informations d’identification à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9f216-772">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-772">5 seconds</span></span> | <span data-ttu-id="9f216-773">WebSockets seulement.</span><span class="sxs-lookup"><span data-stu-id="9f216-773">WebSockets only.</span></span> <span data-ttu-id="9f216-774">Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche.</span><span class="sxs-lookup"><span data-stu-id="9f216-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9f216-775">Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="9f216-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9f216-776">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-776">Empty</span></span> | <span data-ttu-id="9f216-777">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9f216-778">Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9f216-779">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="9f216-780">Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f216-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9f216-781">Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="9f216-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9f216-782">**Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="9f216-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9f216-783">Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9f216-784">Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9f216-785">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="9f216-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9f216-786">Un délégué qui peut être utilisé pour configurer d’autres options WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9f216-787">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="9f216-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9f216-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-789">JavaScript Option</span><span class="sxs-lookup"><span data-stu-id="9f216-789">JavaScript Option</span></span> | <span data-ttu-id="9f216-790">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-790">Default Value</span></span> | <span data-ttu-id="9f216-791">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9f216-792">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9f216-793">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-794">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-795">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-796">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-796">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-797">Java Option</span><span class="sxs-lookup"><span data-stu-id="9f216-797">Java Option</span></span> | <span data-ttu-id="9f216-798">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-798">Default Value</span></span> | <span data-ttu-id="9f216-799">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9f216-800">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9f216-801">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-802">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-803">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9f216-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9f216-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9f216-805">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-805">Empty</span></span> | <span data-ttu-id="9f216-806">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9f216-807">Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :</span><span class="sxs-lookup"><span data-stu-id="9f216-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9f216-808">Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9f216-809">Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9f216-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9f216-810">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9f216-811">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9f216-812">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9f216-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9f216-813">Chaque protocole a des options de configuration de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="9f216-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9f216-814">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode `Startup.ConfigureServices` [d’extension AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9f216-815">La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet.</span><span class="sxs-lookup"><span data-stu-id="9f216-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9f216-816">La [propriété PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet `JsonSerializerSettings` est un objet JSON.NET qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="9f216-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9f216-817">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9f216-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9f216-818">À titre d’exemple, pour configurer le sérialisateur pour utiliser les noms de propriété "PascalCase", au lieu des noms par défaut "camelCase", utilisez le code suivant dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f216-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9f216-819">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9f216-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9f216-820">L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9f216-821">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9f216-822">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="9f216-822">MessagePack serialization options</span></span>

<span data-ttu-id="9f216-823">La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="9f216-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9f216-824">Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="9f216-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-825">Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="9f216-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9f216-826">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="9f216-826">Configure server options</span></span>

<span data-ttu-id="9f216-827">Le tableau suivant décrit les options de configuration des moyeux SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9f216-828">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-828">Option</span></span> | <span data-ttu-id="9f216-829">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-829">Default Value</span></span> | <span data-ttu-id="9f216-830">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9f216-831">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-831">15 seconds</span></span> | <span data-ttu-id="9f216-832">Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="9f216-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9f216-833">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-834">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9f216-835">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-835">15 seconds</span></span> | <span data-ttu-id="9f216-836">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="9f216-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9f216-837">Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="9f216-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9f216-838">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9f216-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9f216-839">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="9f216-839">All installed protocols</span></span> | <span data-ttu-id="9f216-840">Protocoles soutenus par ce hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-840">Protocols supported by this hub.</span></span> <span data-ttu-id="9f216-841">Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="9f216-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9f216-842">Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9f216-843">La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="9f216-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9f216-844">Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .</span><span class="sxs-lookup"><span data-stu-id="9f216-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9f216-845">Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="9f216-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9f216-846">Options avancées de configuration HTTP</span><span class="sxs-lookup"><span data-stu-id="9f216-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9f216-847">Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9f216-848">Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans .</span><span class="sxs-lookup"><span data-stu-id="9f216-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9f216-849">Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="9f216-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9f216-850">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-850">Option</span></span> | <span data-ttu-id="9f216-851">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-851">Default Value</span></span> | <span data-ttu-id="9f216-852">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9f216-853">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-853">32 KB</span></span> | <span data-ttu-id="9f216-854">Le nombre maximum d’octets reçus du client que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="9f216-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9f216-855">L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9f216-856">Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9f216-857">Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub.</span><span class="sxs-lookup"><span data-stu-id="9f216-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9f216-858">32 Ko</span><span class="sxs-lookup"><span data-stu-id="9f216-858">32 KB</span></span> | <span data-ttu-id="9f216-859">Le nombre maximum d’octets envoyés par l’application que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="9f216-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9f216-860">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="9f216-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9f216-861">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="9f216-861">All Transports are enabled.</span></span> | <span data-ttu-id="9f216-862">Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="9f216-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9f216-863">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-863">See below.</span></span> | <span data-ttu-id="9f216-864">Options supplémentaires spécifiques au transport à long scrutin.</span><span class="sxs-lookup"><span data-stu-id="9f216-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9f216-865">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9f216-865">See below.</span></span> | <span data-ttu-id="9f216-866">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9f216-867">Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9f216-868">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-868">Option</span></span> | <span data-ttu-id="9f216-869">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-869">Default Value</span></span> | <span data-ttu-id="9f216-870">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9f216-871">90 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-871">90 seconds</span></span> | <span data-ttu-id="9f216-872">Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9f216-873">La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage.</span><span class="sxs-lookup"><span data-stu-id="9f216-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9f216-874">Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="9f216-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9f216-875">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-875">Option</span></span> | <span data-ttu-id="9f216-876">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-876">Default Value</span></span> | <span data-ttu-id="9f216-877">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9f216-878">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-878">5 seconds</span></span> | <span data-ttu-id="9f216-879">Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="9f216-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9f216-880">Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="9f216-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9f216-881">Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="9f216-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9f216-882">Configurer les options client</span><span class="sxs-lookup"><span data-stu-id="9f216-882">Configure client options</span></span>

<span data-ttu-id="9f216-883">Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9f216-884">Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="9f216-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9f216-885">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="9f216-885">Configure logging</span></span>

<span data-ttu-id="9f216-886">L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9f216-887">Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9f216-888">Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="9f216-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9f216-889">Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires.</span><span class="sxs-lookup"><span data-stu-id="9f216-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9f216-890">Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.</span><span class="sxs-lookup"><span data-stu-id="9f216-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9f216-891">Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="9f216-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9f216-892">Appelez `AddConsole` la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="9f216-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9f216-893">Dans le client JavaScript, une méthode similaire `configureLogging` existe.</span><span class="sxs-lookup"><span data-stu-id="9f216-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9f216-894">Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire.</span><span class="sxs-lookup"><span data-stu-id="9f216-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9f216-895">Les journaux sont écrits sur la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9f216-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9f216-896">Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="9f216-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9f216-897">Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9f216-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9f216-898">Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="9f216-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9f216-899">Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière.</span><span class="sxs-lookup"><span data-stu-id="9f216-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9f216-900">L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="9f216-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9f216-901">Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="9f216-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9f216-902">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="9f216-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9f216-903">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="9f216-903">Configure allowed transports</span></span>

<span data-ttu-id="9f216-904">Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9f216-905">Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="9f216-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9f216-906">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="9f216-906">All transports are enabled by default.</span></span>

<span data-ttu-id="9f216-907">Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :</span><span class="sxs-lookup"><span data-stu-id="9f216-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9f216-908">Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9f216-909">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="9f216-909">Configure bearer authentication</span></span>

<span data-ttu-id="9f216-910">Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="9f216-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9f216-911">Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ).</span><span class="sxs-lookup"><span data-stu-id="9f216-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9f216-912">Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web).</span><span class="sxs-lookup"><span data-stu-id="9f216-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9f216-913">Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .</span><span class="sxs-lookup"><span data-stu-id="9f216-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9f216-914">Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :</span><span class="sxs-lookup"><span data-stu-id="9f216-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9f216-915">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="9f216-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9f216-916">Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9f216-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9f216-917">Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="9f216-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9f216-918">Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="9f216-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9f216-919">Configurer le délai d’attente et les options de maintien en vie</span><span class="sxs-lookup"><span data-stu-id="9f216-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9f216-920">D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:</span><span class="sxs-lookup"><span data-stu-id="9f216-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-921">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-922">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-922">Option</span></span> | <span data-ttu-id="9f216-923">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-923">Default value</span></span> | <span data-ttu-id="9f216-924">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9f216-925">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-926">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-926">Timeout for server activity.</span></span> <span data-ttu-id="9f216-927">Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-928">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-929">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9f216-930">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-930">15 seconds</span></span> | <span data-ttu-id="9f216-931">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-932">Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9f216-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9f216-933">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-934">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9f216-935">Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.</span><span class="sxs-lookup"><span data-stu-id="9f216-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9f216-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-937">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-937">Option</span></span> | <span data-ttu-id="9f216-938">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-938">Default value</span></span> | <span data-ttu-id="9f216-939">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9f216-940">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-941">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-941">Timeout for server activity.</span></span> <span data-ttu-id="9f216-942">Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9f216-943">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-944">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-945">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-945">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-946">Option</span><span class="sxs-lookup"><span data-stu-id="9f216-946">Option</span></span> | <span data-ttu-id="9f216-947">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-947">Default value</span></span> | <span data-ttu-id="9f216-948">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9f216-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9f216-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9f216-950">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="9f216-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9f216-951">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-951">Timeout for server activity.</span></span> <span data-ttu-id="9f216-952">Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-953">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="9f216-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9f216-954">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur, pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="9f216-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9f216-955">15 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-955">15 seconds</span></span> | <span data-ttu-id="9f216-956">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="9f216-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="9f216-957">Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="9f216-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9f216-958">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="9f216-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9f216-959">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9f216-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9f216-960">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-960">Configure additional options</span></span>

<span data-ttu-id="9f216-961">D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :</span><span class="sxs-lookup"><span data-stu-id="9f216-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9f216-962">.NET</span><span class="sxs-lookup"><span data-stu-id="9f216-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9f216-963">Option .NET</span><span class="sxs-lookup"><span data-stu-id="9f216-963">.NET Option</span></span> |  <span data-ttu-id="9f216-964">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-964">Default value</span></span> | <span data-ttu-id="9f216-965">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9f216-966">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9f216-967">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-968">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-969">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9f216-970">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-970">Empty</span></span> | <span data-ttu-id="9f216-971">Une collection de certificats TLS à envoyer pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="9f216-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9f216-972">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-972">Empty</span></span> | <span data-ttu-id="9f216-973">Une collection de cookies HTTP à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9f216-974">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-974">Empty</span></span> | <span data-ttu-id="9f216-975">Informations d’identification à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9f216-976">5 secondes</span><span class="sxs-lookup"><span data-stu-id="9f216-976">5 seconds</span></span> | <span data-ttu-id="9f216-977">WebSockets seulement.</span><span class="sxs-lookup"><span data-stu-id="9f216-977">WebSockets only.</span></span> <span data-ttu-id="9f216-978">Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche.</span><span class="sxs-lookup"><span data-stu-id="9f216-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9f216-979">Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="9f216-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9f216-980">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-980">Empty</span></span> | <span data-ttu-id="9f216-981">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9f216-982">Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9f216-983">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="9f216-984">Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="9f216-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9f216-985">Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="9f216-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9f216-986">**Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="9f216-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9f216-987">Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9f216-988">Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9f216-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9f216-989">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="9f216-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9f216-990">Un délégué qui peut être utilisé pour configurer d’autres options WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9f216-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9f216-991">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="9f216-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9f216-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9f216-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9f216-993">JavaScript Option</span><span class="sxs-lookup"><span data-stu-id="9f216-993">JavaScript Option</span></span> | <span data-ttu-id="9f216-994">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-994">Default Value</span></span> | <span data-ttu-id="9f216-995">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9f216-996">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9f216-997">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-998">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-999">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9f216-1000">Java</span><span class="sxs-lookup"><span data-stu-id="9f216-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="9f216-1001">Java Option</span><span class="sxs-lookup"><span data-stu-id="9f216-1001">Java Option</span></span> | <span data-ttu-id="9f216-1002">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="9f216-1002">Default Value</span></span> | <span data-ttu-id="9f216-1003">Description</span><span class="sxs-lookup"><span data-stu-id="9f216-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9f216-1004">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9f216-1005">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="9f216-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9f216-1006">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="9f216-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9f216-1007">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="9f216-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9f216-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9f216-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9f216-1009">Vide</span><span class="sxs-lookup"><span data-stu-id="9f216-1009">Empty</span></span> | <span data-ttu-id="9f216-1010">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f216-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9f216-1011">Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :</span><span class="sxs-lookup"><span data-stu-id="9f216-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9f216-1012">Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :</span><span class="sxs-lookup"><span data-stu-id="9f216-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9f216-1013">Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9f216-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9f216-1014">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f216-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
