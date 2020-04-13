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
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="fa524-103">Configuration d’ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="fa524-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fa524-104">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="fa524-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fa524-105">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="fa524-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fa524-106">Chaque protocole a des options de configuration de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="fa524-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="fa524-107">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode [d’extension AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="fa524-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="fa524-108">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa524-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fa524-109">La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet.</span><span class="sxs-lookup"><span data-stu-id="fa524-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fa524-110">La [propriété PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> cet objet est un objet qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="fa524-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fa524-111">Pour plus d’informations, voir la [documentation System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="fa524-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="fa524-112">Par exemple, pour configurer le sérialisateur pour ne pas modifier le boîtier des noms de `Startup.ConfigureServices`propriété, au lieu des noms par défaut "camelCase", utilisez le code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="fa524-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="fa524-113">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="fa524-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fa524-114">L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="fa524-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="fa524-115">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="fa524-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="fa524-116">Passez à Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="fa524-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="fa524-117">Si vous avez `Newtonsoft.Json` besoin de fonctionnalités de ce qui ne sont pas pris en charge , `System.Text.Json`Voir Passer à [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="fa524-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fa524-118">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="fa524-118">MessagePack serialization options</span></span>

<span data-ttu-id="fa524-119">La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="fa524-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fa524-120">Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="fa524-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fa524-121">Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="fa524-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fa524-122">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="fa524-122">Configure server options</span></span>

<span data-ttu-id="fa524-123">Le tableau suivant décrit les options de configuration des moyeux SignalR :</span><span class="sxs-lookup"><span data-stu-id="fa524-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="fa524-124">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-124">Option</span></span> | <span data-ttu-id="fa524-125">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-125">Default Value</span></span> | <span data-ttu-id="fa524-126">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fa524-127">30 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-127">30 seconds</span></span> | <span data-ttu-id="fa524-128">Le serveur considérera le client déconnecté s’il n’a pas reçu de message (y compris le maintien en vie) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fa524-129">Il pourrait prendre plus de temps que cet intervalle de délai d’attente pour le client d’être effectivement marqué déconnecté, en raison de la façon dont cela est mis en œuvre.</span><span class="sxs-lookup"><span data-stu-id="fa524-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fa524-130">La valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="fa524-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fa524-131">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-131">15 seconds</span></span> | <span data-ttu-id="fa524-132">Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="fa524-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fa524-133">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-134">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fa524-135">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-135">15 seconds</span></span> | <span data-ttu-id="fa524-136">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="fa524-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fa524-137">Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="fa524-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fa524-138">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="fa524-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fa524-139">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="fa524-139">All installed protocols</span></span> | <span data-ttu-id="fa524-140">Protocoles soutenus par ce hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-140">Protocols supported by this hub.</span></span> <span data-ttu-id="fa524-141">Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="fa524-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fa524-142">Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fa524-143">La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="fa524-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="fa524-144">Le nombre maximum d’éléments qui peuvent être tamponnés pour les flux de téléchargement des clients.</span><span class="sxs-lookup"><span data-stu-id="fa524-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="fa524-145">Si cette limite est atteinte, le traitement des invocations est bloqué jusqu’à ce que le serveur traite les éléments du flux.</span><span class="sxs-lookup"><span data-stu-id="fa524-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="fa524-146">32 Ko</span><span class="sxs-lookup"><span data-stu-id="fa524-146">32 KB</span></span> | <span data-ttu-id="fa524-147">Taille maximale d’un seul message de hub entrant.</span><span class="sxs-lookup"><span data-stu-id="fa524-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="fa524-148">Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .</span><span class="sxs-lookup"><span data-stu-id="fa524-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="fa524-149">Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="fa524-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fa524-150">Options avancées de configuration HTTP</span><span class="sxs-lookup"><span data-stu-id="fa524-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="fa524-151">Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fa524-152">Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans .</span><span class="sxs-lookup"><span data-stu-id="fa524-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="fa524-153">Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="fa524-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="fa524-154">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-154">Option</span></span> | <span data-ttu-id="fa524-155">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-155">Default Value</span></span> | <span data-ttu-id="fa524-156">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fa524-157">32 Ko</span><span class="sxs-lookup"><span data-stu-id="fa524-157">32 KB</span></span> | <span data-ttu-id="fa524-158">Le nombre maximum d’octets reçus du client que le serveur tampons avant d’appliquer backpressure.</span><span class="sxs-lookup"><span data-stu-id="fa524-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="fa524-159">L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands plus rapidement sans appliquer la rétropression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fa524-160">Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fa524-161">Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fa524-162">32 Ko</span><span class="sxs-lookup"><span data-stu-id="fa524-162">32 KB</span></span> | <span data-ttu-id="fa524-163">Le nombre maximum d’octets envoyés par l’application que le serveur tampons avant d’observer la rétropression.</span><span class="sxs-lookup"><span data-stu-id="fa524-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="fa524-164">L’augmentation de cette valeur permet au serveur de tamponner les messages plus grands plus rapidement sans attendre backpressure, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fa524-165">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="fa524-165">All Transports are enabled.</span></span> | <span data-ttu-id="fa524-166">Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="fa524-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fa524-167">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa524-167">See below.</span></span> | <span data-ttu-id="fa524-168">Options supplémentaires spécifiques au transport à long scrutin.</span><span class="sxs-lookup"><span data-stu-id="fa524-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fa524-169">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa524-169">See below.</span></span> | <span data-ttu-id="fa524-170">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fa524-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="fa524-171">Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="fa524-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fa524-172">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-172">Option</span></span> | <span data-ttu-id="fa524-173">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-173">Default Value</span></span> | <span data-ttu-id="fa524-174">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fa524-175">90 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-175">90 seconds</span></span> | <span data-ttu-id="fa524-176">Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage.</span><span class="sxs-lookup"><span data-stu-id="fa524-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fa524-177">La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage.</span><span class="sxs-lookup"><span data-stu-id="fa524-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fa524-178">Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="fa524-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fa524-179">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-179">Option</span></span> | <span data-ttu-id="fa524-180">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-180">Default Value</span></span> | <span data-ttu-id="fa524-181">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fa524-182">5 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-182">5 seconds</span></span> | <span data-ttu-id="fa524-183">Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="fa524-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fa524-184">Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="fa524-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fa524-185">Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="fa524-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fa524-186">Configurer les options client</span><span class="sxs-lookup"><span data-stu-id="fa524-186">Configure client options</span></span>

<span data-ttu-id="fa524-187">Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fa524-188">Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="fa524-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fa524-189">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="fa524-189">Configure logging</span></span>

<span data-ttu-id="fa524-190">L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fa524-191">Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fa524-192">Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="fa524-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fa524-193">Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires.</span><span class="sxs-lookup"><span data-stu-id="fa524-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fa524-194">Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.</span><span class="sxs-lookup"><span data-stu-id="fa524-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fa524-195">Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="fa524-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fa524-196">Appelez `AddConsole` la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="fa524-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fa524-197">Dans le client JavaScript, une méthode similaire `configureLogging` existe.</span><span class="sxs-lookup"><span data-stu-id="fa524-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fa524-198">Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire.</span><span class="sxs-lookup"><span data-stu-id="fa524-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fa524-199">Les journaux sont écrits sur la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="fa524-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="fa524-200">Au lieu `LogLevel` d’une valeur, `string` vous pouvez également fournir une valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="fa524-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="fa524-201">Ceci est utile lors de la configuration de l’enregistrement SignalR `LogLevel` dans des environnements où vous n’avez pas accès aux constantes.</span><span class="sxs-lookup"><span data-stu-id="fa524-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="fa524-202">Le tableau suivant répertorie les niveaux de journal disponibles.</span><span class="sxs-lookup"><span data-stu-id="fa524-202">The following table lists the available log levels.</span></span> <span data-ttu-id="fa524-203">La valeur que `configureLogging` vous fournissez pour établir le niveau **minimum** de journal qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="fa524-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="fa524-204">Les messages enregistrés à ce niveau, **ou les niveaux énumérés après lui dans le tableau,** seront enregistrés.</span><span class="sxs-lookup"><span data-stu-id="fa524-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="fa524-205">String</span><span class="sxs-lookup"><span data-stu-id="fa524-205">String</span></span>                      | <span data-ttu-id="fa524-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="fa524-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="fa524-207">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="fa524-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="fa524-208">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="fa524-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="fa524-209">Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fa524-210">Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="fa524-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fa524-211">Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="fa524-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fa524-212">Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière.</span><span class="sxs-lookup"><span data-stu-id="fa524-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fa524-213">L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="fa524-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fa524-214">Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="fa524-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fa524-215">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="fa524-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fa524-216">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="fa524-216">Configure allowed transports</span></span>

<span data-ttu-id="fa524-217">Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fa524-218">Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="fa524-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fa524-219">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="fa524-219">All transports are enabled by default.</span></span>

<span data-ttu-id="fa524-220">Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :</span><span class="sxs-lookup"><span data-stu-id="fa524-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fa524-221">Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :</span><span class="sxs-lookup"><span data-stu-id="fa524-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="fa524-222">Dans cette version des websockets clients Java est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="fa524-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="fa524-223">Dans le client Java, le `withTransport` transport est `HttpHubConnectionBuilder`sélectionné avec la méthode sur le .</span><span class="sxs-lookup"><span data-stu-id="fa524-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="fa524-224">Le client Java par défaut à l’utilisation du transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fa524-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fa524-225">Le client SignalR Java ne prend pas encore en charge le repli des transports.</span><span class="sxs-lookup"><span data-stu-id="fa524-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fa524-226">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="fa524-226">Configure bearer authentication</span></span>

<span data-ttu-id="fa524-227">Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="fa524-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fa524-228">Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ).</span><span class="sxs-lookup"><span data-stu-id="fa524-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fa524-229">Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web).</span><span class="sxs-lookup"><span data-stu-id="fa524-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fa524-230">Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .</span><span class="sxs-lookup"><span data-stu-id="fa524-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fa524-231">Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :</span><span class="sxs-lookup"><span data-stu-id="fa524-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fa524-232">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="fa524-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="fa524-233">Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="fa524-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fa524-234">Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="fa524-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fa524-235">Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="fa524-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fa524-236">Configurer le délai d’attente et les options de maintien en vie</span><span class="sxs-lookup"><span data-stu-id="fa524-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fa524-237">D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:</span><span class="sxs-lookup"><span data-stu-id="fa524-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="fa524-238">.NET</span><span class="sxs-lookup"><span data-stu-id="fa524-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fa524-239">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-239">Option</span></span> | <span data-ttu-id="fa524-240">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-240">Default value</span></span> | <span data-ttu-id="fa524-241">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fa524-242">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-243">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-243">Timeout for server activity.</span></span> <span data-ttu-id="fa524-244">Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fa524-245">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-246">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fa524-247">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-247">15 seconds</span></span> | <span data-ttu-id="fa524-248">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="fa524-249">Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fa524-250">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-251">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fa524-252">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-252">15 seconds</span></span> | <span data-ttu-id="fa524-253">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="fa524-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fa524-254">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fa524-255">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="fa524-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="fa524-256">Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.</span><span class="sxs-lookup"><span data-stu-id="fa524-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="fa524-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fa524-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fa524-258">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-258">Option</span></span> | <span data-ttu-id="fa524-259">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-259">Default value</span></span> | <span data-ttu-id="fa524-260">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fa524-261">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-262">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-262">Timeout for server activity.</span></span> <span data-ttu-id="fa524-263">Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fa524-264">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-265">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="fa524-266">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fa524-267">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="fa524-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fa524-268">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fa524-269">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="fa524-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fa524-270">Java</span><span class="sxs-lookup"><span data-stu-id="fa524-270">Java</span></span>](#tab/java)

| <span data-ttu-id="fa524-271">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-271">Option</span></span> | <span data-ttu-id="fa524-272">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-272">Default value</span></span> | <span data-ttu-id="fa524-273">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fa524-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fa524-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fa524-275">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-276">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-276">Timeout for server activity.</span></span> <span data-ttu-id="fa524-277">Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fa524-278">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-279">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fa524-280">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-280">15 seconds</span></span> | <span data-ttu-id="fa524-281">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="fa524-282">Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fa524-283">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-284">Pour plus de détails sur le processus de poignée de main, consultez la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="fa524-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="fa524-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="fa524-286">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fa524-287">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="fa524-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fa524-288">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fa524-289">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="fa524-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="fa524-290">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fa524-290">Configure additional options</span></span>

<span data-ttu-id="fa524-291">D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :</span><span class="sxs-lookup"><span data-stu-id="fa524-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="fa524-292">.NET</span><span class="sxs-lookup"><span data-stu-id="fa524-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fa524-293">Option .NET</span><span class="sxs-lookup"><span data-stu-id="fa524-293">.NET Option</span></span> |  <span data-ttu-id="fa524-294">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-294">Default value</span></span> | <span data-ttu-id="fa524-295">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fa524-296">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fa524-297">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-298">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-299">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fa524-300">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-300">Empty</span></span> | <span data-ttu-id="fa524-301">Une collection de certificats TLS à envoyer pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="fa524-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fa524-302">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-302">Empty</span></span> | <span data-ttu-id="fa524-303">Une collection de cookies HTTP à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fa524-304">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-304">Empty</span></span> | <span data-ttu-id="fa524-305">Informations d’identification à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fa524-306">5 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-306">5 seconds</span></span> | <span data-ttu-id="fa524-307">WebSockets seulement.</span><span class="sxs-lookup"><span data-stu-id="fa524-307">WebSockets only.</span></span> <span data-ttu-id="fa524-308">Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche.</span><span class="sxs-lookup"><span data-stu-id="fa524-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fa524-309">Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="fa524-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fa524-310">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-310">Empty</span></span> | <span data-ttu-id="fa524-311">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fa524-312">Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fa524-313">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fa524-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="fa524-314">Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="fa524-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fa524-315">Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="fa524-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fa524-316">**Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="fa524-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fa524-317">Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fa524-318">Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fa524-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fa524-319">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="fa524-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fa524-320">Un délégué qui peut être utilisé pour configurer d’autres options WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fa524-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fa524-321">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="fa524-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="fa524-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fa524-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fa524-323">JavaScript Option</span><span class="sxs-lookup"><span data-stu-id="fa524-323">JavaScript Option</span></span> | <span data-ttu-id="fa524-324">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-324">Default Value</span></span> | <span data-ttu-id="fa524-325">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fa524-326">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fa524-327">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-328">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-329">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fa524-330">Java</span><span class="sxs-lookup"><span data-stu-id="fa524-330">Java</span></span>](#tab/java)

| <span data-ttu-id="fa524-331">Java Option</span><span class="sxs-lookup"><span data-stu-id="fa524-331">Java Option</span></span> | <span data-ttu-id="fa524-332">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-332">Default Value</span></span> | <span data-ttu-id="fa524-333">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fa524-334">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fa524-335">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-336">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-337">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fa524-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fa524-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fa524-339">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-339">Empty</span></span> | <span data-ttu-id="fa524-340">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fa524-341">Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :</span><span class="sxs-lookup"><span data-stu-id="fa524-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fa524-342">Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :</span><span class="sxs-lookup"><span data-stu-id="fa524-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fa524-343">Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="fa524-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fa524-344">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fa524-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fa524-345">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="fa524-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fa524-346">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="fa524-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fa524-347">Chaque protocole a des options de configuration de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="fa524-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="fa524-348">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode `Startup.ConfigureServices` [d’extension AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fa524-349">La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet.</span><span class="sxs-lookup"><span data-stu-id="fa524-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fa524-350">La [propriété PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet `JsonSerializerSettings` est un objet JSON.NET qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="fa524-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fa524-351">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="fa524-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="fa524-352">À titre d’exemple, pour configurer le sérialisateur pour utiliser les noms de propriété "PascalCase", au lieu des noms par défaut "camelCase", utilisez le code suivant dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa524-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="fa524-353">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="fa524-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fa524-354">L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="fa524-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="fa524-355">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="fa524-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fa524-356">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="fa524-356">MessagePack serialization options</span></span>

<span data-ttu-id="fa524-357">La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="fa524-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fa524-358">Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="fa524-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fa524-359">Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="fa524-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fa524-360">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="fa524-360">Configure server options</span></span>

<span data-ttu-id="fa524-361">Le tableau suivant décrit les options de configuration des moyeux SignalR :</span><span class="sxs-lookup"><span data-stu-id="fa524-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="fa524-362">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-362">Option</span></span> | <span data-ttu-id="fa524-363">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-363">Default Value</span></span> | <span data-ttu-id="fa524-364">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fa524-365">30 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-365">30 seconds</span></span> | <span data-ttu-id="fa524-366">Le serveur considérera le client déconnecté s’il n’a pas reçu de message (y compris le maintien en vie) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fa524-367">Il pourrait prendre plus de temps que cet intervalle de délai d’attente pour le client d’être effectivement marqué déconnecté, en raison de la façon dont cela est mis en œuvre.</span><span class="sxs-lookup"><span data-stu-id="fa524-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fa524-368">La valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="fa524-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fa524-369">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-369">15 seconds</span></span> | <span data-ttu-id="fa524-370">Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="fa524-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fa524-371">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-372">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fa524-373">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-373">15 seconds</span></span> | <span data-ttu-id="fa524-374">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="fa524-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fa524-375">Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="fa524-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fa524-376">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="fa524-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fa524-377">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="fa524-377">All installed protocols</span></span> | <span data-ttu-id="fa524-378">Protocoles soutenus par ce hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-378">Protocols supported by this hub.</span></span> <span data-ttu-id="fa524-379">Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="fa524-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fa524-380">Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fa524-381">La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="fa524-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="fa524-382">Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .</span><span class="sxs-lookup"><span data-stu-id="fa524-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="fa524-383">Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="fa524-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fa524-384">Options avancées de configuration HTTP</span><span class="sxs-lookup"><span data-stu-id="fa524-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="fa524-385">Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fa524-386">Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans .</span><span class="sxs-lookup"><span data-stu-id="fa524-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="fa524-387">Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="fa524-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="fa524-388">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-388">Option</span></span> | <span data-ttu-id="fa524-389">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-389">Default Value</span></span> | <span data-ttu-id="fa524-390">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fa524-391">32 Ko</span><span class="sxs-lookup"><span data-stu-id="fa524-391">32 KB</span></span> | <span data-ttu-id="fa524-392">Le nombre maximum d’octets reçus du client que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="fa524-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="fa524-393">L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fa524-394">Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fa524-395">Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fa524-396">32 Ko</span><span class="sxs-lookup"><span data-stu-id="fa524-396">32 KB</span></span> | <span data-ttu-id="fa524-397">Le nombre maximum d’octets envoyés par l’application que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="fa524-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="fa524-398">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fa524-399">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="fa524-399">All Transports are enabled.</span></span> | <span data-ttu-id="fa524-400">Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="fa524-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fa524-401">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa524-401">See below.</span></span> | <span data-ttu-id="fa524-402">Options supplémentaires spécifiques au transport à long scrutin.</span><span class="sxs-lookup"><span data-stu-id="fa524-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fa524-403">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa524-403">See below.</span></span> | <span data-ttu-id="fa524-404">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fa524-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="fa524-405">Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="fa524-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fa524-406">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-406">Option</span></span> | <span data-ttu-id="fa524-407">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-407">Default Value</span></span> | <span data-ttu-id="fa524-408">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fa524-409">90 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-409">90 seconds</span></span> | <span data-ttu-id="fa524-410">Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage.</span><span class="sxs-lookup"><span data-stu-id="fa524-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fa524-411">La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage.</span><span class="sxs-lookup"><span data-stu-id="fa524-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fa524-412">Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="fa524-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fa524-413">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-413">Option</span></span> | <span data-ttu-id="fa524-414">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-414">Default Value</span></span> | <span data-ttu-id="fa524-415">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fa524-416">5 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-416">5 seconds</span></span> | <span data-ttu-id="fa524-417">Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="fa524-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fa524-418">Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="fa524-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fa524-419">Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="fa524-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fa524-420">Configurer les options client</span><span class="sxs-lookup"><span data-stu-id="fa524-420">Configure client options</span></span>

<span data-ttu-id="fa524-421">Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fa524-422">Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="fa524-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fa524-423">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="fa524-423">Configure logging</span></span>

<span data-ttu-id="fa524-424">L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fa524-425">Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fa524-426">Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="fa524-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fa524-427">Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires.</span><span class="sxs-lookup"><span data-stu-id="fa524-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fa524-428">Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.</span><span class="sxs-lookup"><span data-stu-id="fa524-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fa524-429">Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="fa524-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fa524-430">Appelez `AddConsole` la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="fa524-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fa524-431">Dans le client JavaScript, une méthode similaire `configureLogging` existe.</span><span class="sxs-lookup"><span data-stu-id="fa524-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fa524-432">Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire.</span><span class="sxs-lookup"><span data-stu-id="fa524-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fa524-433">Les journaux sont écrits sur la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="fa524-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fa524-434">Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fa524-435">Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="fa524-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fa524-436">Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="fa524-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fa524-437">Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière.</span><span class="sxs-lookup"><span data-stu-id="fa524-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fa524-438">L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="fa524-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fa524-439">Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="fa524-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fa524-440">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="fa524-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fa524-441">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="fa524-441">Configure allowed transports</span></span>

<span data-ttu-id="fa524-442">Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fa524-443">Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="fa524-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fa524-444">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="fa524-444">All transports are enabled by default.</span></span>

<span data-ttu-id="fa524-445">Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :</span><span class="sxs-lookup"><span data-stu-id="fa524-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fa524-446">Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :</span><span class="sxs-lookup"><span data-stu-id="fa524-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="fa524-447">Dans cette version des websockets clients Java est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="fa524-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fa524-448">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="fa524-448">Configure bearer authentication</span></span>

<span data-ttu-id="fa524-449">Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="fa524-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fa524-450">Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ).</span><span class="sxs-lookup"><span data-stu-id="fa524-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fa524-451">Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web).</span><span class="sxs-lookup"><span data-stu-id="fa524-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fa524-452">Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .</span><span class="sxs-lookup"><span data-stu-id="fa524-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fa524-453">Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :</span><span class="sxs-lookup"><span data-stu-id="fa524-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fa524-454">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="fa524-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="fa524-455">Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="fa524-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fa524-456">Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="fa524-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fa524-457">Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="fa524-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fa524-458">Configurer le délai d’attente et les options de maintien en vie</span><span class="sxs-lookup"><span data-stu-id="fa524-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fa524-459">D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:</span><span class="sxs-lookup"><span data-stu-id="fa524-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="fa524-460">.NET</span><span class="sxs-lookup"><span data-stu-id="fa524-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fa524-461">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-461">Option</span></span> | <span data-ttu-id="fa524-462">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-462">Default value</span></span> | <span data-ttu-id="fa524-463">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fa524-464">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-465">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-465">Timeout for server activity.</span></span> <span data-ttu-id="fa524-466">Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fa524-467">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-468">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fa524-469">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-469">15 seconds</span></span> | <span data-ttu-id="fa524-470">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="fa524-471">Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fa524-472">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-473">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fa524-474">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-474">15 seconds</span></span> | <span data-ttu-id="fa524-475">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="fa524-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fa524-476">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fa524-477">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="fa524-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="fa524-478">Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.</span><span class="sxs-lookup"><span data-stu-id="fa524-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="fa524-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fa524-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fa524-480">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-480">Option</span></span> | <span data-ttu-id="fa524-481">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-481">Default value</span></span> | <span data-ttu-id="fa524-482">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fa524-483">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-484">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-484">Timeout for server activity.</span></span> <span data-ttu-id="fa524-485">Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fa524-486">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-487">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="fa524-488">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fa524-489">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="fa524-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fa524-490">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fa524-491">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="fa524-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fa524-492">Java</span><span class="sxs-lookup"><span data-stu-id="fa524-492">Java</span></span>](#tab/java)

| <span data-ttu-id="fa524-493">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-493">Option</span></span> | <span data-ttu-id="fa524-494">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-494">Default value</span></span> | <span data-ttu-id="fa524-495">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fa524-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fa524-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fa524-497">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-498">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-498">Timeout for server activity.</span></span> <span data-ttu-id="fa524-499">Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fa524-500">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-501">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fa524-502">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-502">15 seconds</span></span> | <span data-ttu-id="fa524-503">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="fa524-504">Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fa524-505">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-506">Pour plus de détails sur le processus de poignée de main, consultez la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="fa524-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="fa524-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="fa524-508">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fa524-509">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="fa524-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fa524-510">Envoi de tout message du client réinitialise la minuterie au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="fa524-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fa524-511">Si le client n’a pas `ClientTimeoutInterval` envoyé de message dans l’ensemble sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="fa524-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="fa524-512">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fa524-512">Configure additional options</span></span>

<span data-ttu-id="fa524-513">D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :</span><span class="sxs-lookup"><span data-stu-id="fa524-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="fa524-514">.NET</span><span class="sxs-lookup"><span data-stu-id="fa524-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fa524-515">Option .NET</span><span class="sxs-lookup"><span data-stu-id="fa524-515">.NET Option</span></span> |  <span data-ttu-id="fa524-516">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-516">Default value</span></span> | <span data-ttu-id="fa524-517">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fa524-518">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fa524-519">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-520">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-521">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fa524-522">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-522">Empty</span></span> | <span data-ttu-id="fa524-523">Une collection de certificats TLS à envoyer pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="fa524-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fa524-524">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-524">Empty</span></span> | <span data-ttu-id="fa524-525">Une collection de cookies HTTP à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fa524-526">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-526">Empty</span></span> | <span data-ttu-id="fa524-527">Informations d’identification à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fa524-528">5 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-528">5 seconds</span></span> | <span data-ttu-id="fa524-529">WebSockets seulement.</span><span class="sxs-lookup"><span data-stu-id="fa524-529">WebSockets only.</span></span> <span data-ttu-id="fa524-530">Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche.</span><span class="sxs-lookup"><span data-stu-id="fa524-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fa524-531">Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="fa524-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fa524-532">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-532">Empty</span></span> | <span data-ttu-id="fa524-533">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fa524-534">Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fa524-535">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fa524-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="fa524-536">Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="fa524-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fa524-537">Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="fa524-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fa524-538">**Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="fa524-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fa524-539">Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fa524-540">Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fa524-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fa524-541">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="fa524-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fa524-542">Un délégué qui peut être utilisé pour configurer d’autres options WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fa524-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fa524-543">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="fa524-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="fa524-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fa524-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fa524-545">JavaScript Option</span><span class="sxs-lookup"><span data-stu-id="fa524-545">JavaScript Option</span></span> | <span data-ttu-id="fa524-546">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-546">Default Value</span></span> | <span data-ttu-id="fa524-547">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fa524-548">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fa524-549">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-550">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-551">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fa524-552">Java</span><span class="sxs-lookup"><span data-stu-id="fa524-552">Java</span></span>](#tab/java)

| <span data-ttu-id="fa524-553">Java Option</span><span class="sxs-lookup"><span data-stu-id="fa524-553">Java Option</span></span> | <span data-ttu-id="fa524-554">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-554">Default Value</span></span> | <span data-ttu-id="fa524-555">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fa524-556">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fa524-557">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-558">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-559">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fa524-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fa524-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fa524-561">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-561">Empty</span></span> | <span data-ttu-id="fa524-562">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fa524-563">Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :</span><span class="sxs-lookup"><span data-stu-id="fa524-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fa524-564">Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :</span><span class="sxs-lookup"><span data-stu-id="fa524-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fa524-565">Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="fa524-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fa524-566">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fa524-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fa524-567">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="fa524-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fa524-568">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="fa524-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fa524-569">Chaque protocole a des options de configuration de sérialisation.</span><span class="sxs-lookup"><span data-stu-id="fa524-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="fa524-570">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode `Startup.ConfigureServices` [d’extension AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fa524-571">La `AddJsonProtocol` méthode prend un `options` délégué qui reçoit un objet.</span><span class="sxs-lookup"><span data-stu-id="fa524-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fa524-572">La [propriété PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet `JsonSerializerSettings` est un objet JSON.NET qui peut être utilisé pour configurer la sérialisation des arguments et des valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="fa524-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fa524-573">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="fa524-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="fa524-574">À titre d’exemple, pour configurer le sérialisateur pour utiliser les noms de propriété "PascalCase", au lieu des noms par défaut "camelCase", utilisez le code suivant dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa524-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="fa524-575">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="fa524-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fa524-576">L’espace `Microsoft.Extensions.DependencyInjection` nom doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="fa524-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="fa524-577">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="fa524-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fa524-578">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="fa524-578">MessagePack serialization options</span></span>

<span data-ttu-id="fa524-579">La sérialisation de MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="fa524-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fa524-580">Voir [MessagePack dans SignalR](xref:signalr/messagepackhubprotocol) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="fa524-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fa524-581">Il n’est pas possible de configurer la sérialisation De MessagePack dans le client JavaScript pour le moment.</span><span class="sxs-lookup"><span data-stu-id="fa524-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fa524-582">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="fa524-582">Configure server options</span></span>

<span data-ttu-id="fa524-583">Le tableau suivant décrit les options de configuration des moyeux SignalR :</span><span class="sxs-lookup"><span data-stu-id="fa524-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="fa524-584">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-584">Option</span></span> | <span data-ttu-id="fa524-585">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-585">Default Value</span></span> | <span data-ttu-id="fa524-586">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="fa524-587">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-587">15 seconds</span></span> | <span data-ttu-id="fa524-588">Si le client n’envoie pas un message de poignée de main initial dans ce délai, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="fa524-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fa524-589">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-590">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fa524-591">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-591">15 seconds</span></span> | <span data-ttu-id="fa524-592">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour garder la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="fa524-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fa524-593">Lors `KeepAliveInterval`du changement, changer le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="fa524-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fa524-594">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée `KeepAliveInterval` est le double de la valeur.</span><span class="sxs-lookup"><span data-stu-id="fa524-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fa524-595">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="fa524-595">All installed protocols</span></span> | <span data-ttu-id="fa524-596">Protocoles soutenus par ce hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-596">Protocols supported by this hub.</span></span> <span data-ttu-id="fa524-597">Par défaut, tous les protocoles enregistrés sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver les protocoles spécifiques pour les hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="fa524-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fa524-598">Si `true`, des messages d’exception détaillés sont retournés aux clients lorsqu’une exception est projetée dans une méthode Hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fa524-599">La valeur `false`par défaut est , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="fa524-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="fa524-600">Les options peuvent être configurées pour tous `AddSignalR` les `Startup.ConfigureServices`hubs en fournissant un délégué d’options à l’appel .</span><span class="sxs-lookup"><span data-stu-id="fa524-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="fa524-601">Les options pour un seul hub `AddSignalR` remplacent les options <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>globales fournies et peuvent être configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="fa524-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fa524-602">Options avancées de configuration HTTP</span><span class="sxs-lookup"><span data-stu-id="fa524-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="fa524-603">Utiliser `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés liés aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fa524-604">Ces options sont configurées en passant un délégué `Startup.Configure`à [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans .</span><span class="sxs-lookup"><span data-stu-id="fa524-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="fa524-605">Le tableau suivant décrit les options de configuration des options HTTP avancées ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="fa524-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="fa524-606">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-606">Option</span></span> | <span data-ttu-id="fa524-607">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-607">Default Value</span></span> | <span data-ttu-id="fa524-608">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fa524-609">32 Ko</span><span class="sxs-lookup"><span data-stu-id="fa524-609">32 KB</span></span> | <span data-ttu-id="fa524-610">Le nombre maximum d’octets reçus du client que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="fa524-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="fa524-611">L’augmentation de cette valeur permet au serveur de recevoir des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fa524-612">Données automatiquement recueillies `Authorize` à partir des attributs appliqués à la classe Hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fa524-613">Une liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au hub.</span><span class="sxs-lookup"><span data-stu-id="fa524-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fa524-614">32 Ko</span><span class="sxs-lookup"><span data-stu-id="fa524-614">32 KB</span></span> | <span data-ttu-id="fa524-615">Le nombre maximum d’octets envoyés par l’application que le serveur tampons.</span><span class="sxs-lookup"><span data-stu-id="fa524-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="fa524-616">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus grands, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="fa524-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fa524-617">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="fa524-617">All Transports are enabled.</span></span> | <span data-ttu-id="fa524-618">Un peu de `HttpTransportType` drapeaux enum de valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="fa524-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fa524-619">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa524-619">See below.</span></span> | <span data-ttu-id="fa524-620">Options supplémentaires spécifiques au transport à long scrutin.</span><span class="sxs-lookup"><span data-stu-id="fa524-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fa524-621">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa524-621">See below.</span></span> | <span data-ttu-id="fa524-622">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fa524-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="fa524-623">Le transport Long Polling dispose d’options `LongPolling` supplémentaires qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="fa524-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fa524-624">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-624">Option</span></span> | <span data-ttu-id="fa524-625">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-625">Default Value</span></span> | <span data-ttu-id="fa524-626">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fa524-627">90 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-627">90 seconds</span></span> | <span data-ttu-id="fa524-628">Le maximum de temps que le serveur attend qu’un message envoie au client avant de mettre fin à une seule demande de sondage.</span><span class="sxs-lookup"><span data-stu-id="fa524-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fa524-629">La diminution de cette valeur amène le client à émettre plus fréquemment de nouvelles demandes de sondage.</span><span class="sxs-lookup"><span data-stu-id="fa524-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fa524-630">Le transport WebSocket dispose d’options supplémentaires `WebSockets` qui peuvent être configurées à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="fa524-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fa524-631">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-631">Option</span></span> | <span data-ttu-id="fa524-632">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-632">Default Value</span></span> | <span data-ttu-id="fa524-633">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fa524-634">5 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-634">5 seconds</span></span> | <span data-ttu-id="fa524-635">Après la fermeture du serveur, si le client ne parvient pas à se fermer dans ce délai, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="fa524-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fa524-636">Un délégué qui peut être `Sec-WebSocket-Protocol` utilisé pour définir l’en-tête à une valeur personnalisée.</span><span class="sxs-lookup"><span data-stu-id="fa524-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fa524-637">Le délégué reçoit les valeurs demandées par le client à titre d’entrée et on s’attend à ce qu’elle retourne la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="fa524-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fa524-638">Configurer les options client</span><span class="sxs-lookup"><span data-stu-id="fa524-638">Configure client options</span></span>

<span data-ttu-id="fa524-639">Les options clients peuvent `HubConnectionBuilder` être configurées sur le type (disponible dans les clients .NET et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fa524-640">Il est également disponible dans le `HttpHubConnectionBuilder` client Java, mais la sous-classe est `HubConnection` ce qui contient les options de configuration du constructeur, ainsi que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="fa524-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fa524-641">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="fa524-641">Configure logging</span></span>

<span data-ttu-id="fa524-642">L’enregistrement est configuré dans `ConfigureLogging` le client .NET en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fa524-643">Les fournisseurs et les filtres d’enregistrement peuvent être enregistrés de la même manière qu’ils le sont sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fa524-644">Consultez la documentation [de Logging in ASP.NET Core](xref:fundamentals/logging/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="fa524-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fa524-645">Afin d’enregistrer les fournisseurs d’enregistrement, vous devez installer les paquets nécessaires.</span><span class="sxs-lookup"><span data-stu-id="fa524-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fa524-646">Consultez la section [des fournisseurs d’exploitation forestière intégrée](xref:fundamentals/logging/index#built-in-logging-providers) des documents pour une liste complète.</span><span class="sxs-lookup"><span data-stu-id="fa524-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fa524-647">Par exemple, pour activer l’enregistrement de console, installez le `Microsoft.Extensions.Logging.Console` paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="fa524-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fa524-648">Appelez `AddConsole` la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="fa524-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fa524-649">Dans le client JavaScript, une méthode similaire `configureLogging` existe.</span><span class="sxs-lookup"><span data-stu-id="fa524-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fa524-650">Fournir `LogLevel` une valeur indiquant le niveau minimum de messages journalaux à produire.</span><span class="sxs-lookup"><span data-stu-id="fa524-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fa524-651">Les journaux sont écrits sur la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="fa524-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fa524-652">Pour désactiver complètement l’enregistrement, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="fa524-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fa524-653">Pour plus d’informations sur l’enregistrement, consultez la [documentation SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="fa524-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fa524-654">Le client SignalR Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la connexion.</span><span class="sxs-lookup"><span data-stu-id="fa524-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fa524-655">Il s’agit d’une API forestière de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation d’enregistrement spécifique en apportant une dépendance spécifique à l’exploitation forestière.</span><span class="sxs-lookup"><span data-stu-id="fa524-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fa524-656">L’extrait de code suivant montre `java.util.logging` comment utiliser avec le client SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="fa524-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fa524-657">Si vous ne configurez pas l’enregistrement dans vos dépendances, SLF4J charge un enregistreur sans opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="fa524-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fa524-658">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="fa524-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fa524-659">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="fa524-659">Configure allowed transports</span></span>

<span data-ttu-id="fa524-660">Les transports utilisés par SignalR peuvent `WithUrl` être`withUrl` configurés dans l’appel (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fa524-661">Un peu plus ou de `HttpTransportType` la valeur de peut être utilisé pour limiter le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="fa524-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fa524-662">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="fa524-662">All transports are enabled by default.</span></span>

<span data-ttu-id="fa524-663">Par exemple, pour désactiver le transport d’événements envoyés par le serveur, mais permettre aux photos Web et aux connexions à long sondage :</span><span class="sxs-lookup"><span data-stu-id="fa524-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fa524-664">Dans le client JavaScript, les transports `transport` sont configurés `withUrl`en définissant le champ sur l’objet d’options fourni à :</span><span class="sxs-lookup"><span data-stu-id="fa524-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fa524-665">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="fa524-665">Configure bearer authentication</span></span>

<span data-ttu-id="fa524-666">Pour fournir des données d’authentification ainsi que des demandes SignalR, utilisez l’option `AccessTokenProvider` (dans`accessTokenFactory` JavaScript) pour spécifier une fonction qui renvoie le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="fa524-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fa524-667">Dans le client .NET, ce jeton d’accès est transmis comme un `Authorization` jeton HTTP `Bearer`"Bearer Authentication" (En utilisant l’en-tête avec un type de ).</span><span class="sxs-lookup"><span data-stu-id="fa524-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fa524-668">Dans le client JavaScript, le jeton d’accès est utilisé comme un jeton Porteur, **sauf** dans quelques cas où les API du navigateur limitent la possibilité d’appliquer des en-têtes (en particulier, dans les demandes d’événements et de photos Web).</span><span class="sxs-lookup"><span data-stu-id="fa524-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fa524-669">Dans ces cas, le jeton d’accès `access_token`est fourni comme une valeur de chaîne de requête .</span><span class="sxs-lookup"><span data-stu-id="fa524-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fa524-670">Dans le client .NET, l’option `AccessTokenProvider` peut `WithUrl`être spécifiée à l’aide des options déléguées dans :</span><span class="sxs-lookup"><span data-stu-id="fa524-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fa524-671">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet options dans `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="fa524-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="fa524-672">Dans le client SignalR Java, vous pouvez configurer un jeton porteur à utiliser pour l’authentification en fournissant une usine de jetons d’accès à la [httpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="fa524-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fa524-673">Utilisez [avecAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [chaîne unique\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="fa524-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fa524-674">Avec un appel à [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire la logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="fa524-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fa524-675">Configurer le délai d’attente et les options de maintien en vie</span><span class="sxs-lookup"><span data-stu-id="fa524-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fa524-676">D’autres options pour configurer le délai d’attente et le comportement de maintien en vie sont disponibles sur l’objet `HubConnection` lui-même:</span><span class="sxs-lookup"><span data-stu-id="fa524-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="fa524-677">.NET</span><span class="sxs-lookup"><span data-stu-id="fa524-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fa524-678">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-678">Option</span></span> | <span data-ttu-id="fa524-679">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-679">Default value</span></span> | <span data-ttu-id="fa524-680">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fa524-681">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-682">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-682">Timeout for server activity.</span></span> <span data-ttu-id="fa524-683">Si le serveur n’a pas envoyé de message dans cet intervalle, `Closed` le`onclose` client considère le serveur déconnecté et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fa524-684">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-685">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fa524-686">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-686">15 seconds</span></span> | <span data-ttu-id="fa524-687">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="fa524-688">Si le serveur n’envoie pas de réponse de poignée de main dans cet `Closed` intervalle,`onclose` le client annule la poignée de main et déclenche l’événement (dans JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fa524-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fa524-689">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-690">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="fa524-691">Dans le client .NET, les `TimeSpan` valeurs de délai d’attente sont spécifiées comme valeurs.</span><span class="sxs-lookup"><span data-stu-id="fa524-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="fa524-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fa524-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fa524-693">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-693">Option</span></span> | <span data-ttu-id="fa524-694">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-694">Default value</span></span> | <span data-ttu-id="fa524-695">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fa524-696">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-697">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-697">Timeout for server activity.</span></span> <span data-ttu-id="fa524-698">Si le serveur n’a pas envoyé de message dans cet intervalle, `onclose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fa524-699">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-700">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fa524-701">Java</span><span class="sxs-lookup"><span data-stu-id="fa524-701">Java</span></span>](#tab/java)

| <span data-ttu-id="fa524-702">Option</span><span class="sxs-lookup"><span data-stu-id="fa524-702">Option</span></span> | <span data-ttu-id="fa524-703">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-703">Default value</span></span> | <span data-ttu-id="fa524-704">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fa524-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fa524-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fa524-706">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="fa524-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fa524-707">Délai d’attente pour l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-707">Timeout for server activity.</span></span> <span data-ttu-id="fa524-708">Si le serveur n’a pas envoyé de message dans cet intervalle, `onClose` le client considère le serveur déconnecté et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fa524-709">Cette valeur doit être assez grande pour qu’un message de ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="fa524-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fa524-710">La valeur recommandée est un nombre au `KeepAliveInterval` moins le double de la valeur du serveur, pour laisser le temps pour pings d’arriver.</span><span class="sxs-lookup"><span data-stu-id="fa524-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fa524-711">15 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-711">15 seconds</span></span> | <span data-ttu-id="fa524-712">Délai pour la poignée de main initiale du serveur.</span><span class="sxs-lookup"><span data-stu-id="fa524-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="fa524-713">Si le serveur n’envoie pas de réponse de poignée de main dans cet `onClose` intervalle, le client annule la poignée de main et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="fa524-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fa524-714">Il s’agit d’un paramètre avancé qui ne devrait être modifié que si des erreurs de temps d’arrêt de poignée de main se produisent en raison d’une latence réseau sévère.</span><span class="sxs-lookup"><span data-stu-id="fa524-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fa524-715">Pour plus de détails sur le processus de poignée de main, voir la [spécification du protocole SignalR Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fa524-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="fa524-716">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fa524-716">Configure additional options</span></span>

<span data-ttu-id="fa524-717">D’autres options peuvent `WithUrl` être`withUrl` configurées dans `HubConnectionBuilder` la méthode (dans JavaScript) sur ou sur les différentes configurations APIs sur le `HttpHubConnectionBuilder` client Java :</span><span class="sxs-lookup"><span data-stu-id="fa524-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="fa524-718">.NET</span><span class="sxs-lookup"><span data-stu-id="fa524-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fa524-719">Option .NET</span><span class="sxs-lookup"><span data-stu-id="fa524-719">.NET Option</span></span> |  <span data-ttu-id="fa524-720">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-720">Default value</span></span> | <span data-ttu-id="fa524-721">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fa524-722">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fa524-723">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-724">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-725">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fa524-726">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-726">Empty</span></span> | <span data-ttu-id="fa524-727">Une collection de certificats TLS à envoyer pour authentifier les demandes.</span><span class="sxs-lookup"><span data-stu-id="fa524-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fa524-728">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-728">Empty</span></span> | <span data-ttu-id="fa524-729">Une collection de cookies HTTP à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fa524-730">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-730">Empty</span></span> | <span data-ttu-id="fa524-731">Informations d’identification à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fa524-732">5 secondes</span><span class="sxs-lookup"><span data-stu-id="fa524-732">5 seconds</span></span> | <span data-ttu-id="fa524-733">WebSockets seulement.</span><span class="sxs-lookup"><span data-stu-id="fa524-733">WebSockets only.</span></span> <span data-ttu-id="fa524-734">Le temps maximum que le client attend après la fermeture pour que le serveur reconnaisse la demande proche.</span><span class="sxs-lookup"><span data-stu-id="fa524-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fa524-735">Si le serveur ne reconnaît pas la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="fa524-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fa524-736">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-736">Empty</span></span> | <span data-ttu-id="fa524-737">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fa524-738">Un délégué qui peut être utilisé `HttpMessageHandler` pour configurer ou remplacer l’utilisé pour envoyer des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fa524-739">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fa524-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="fa524-740">Ce délégué doit retourner une valeur non nulle, et il reçoit la valeur par défaut comme paramètre.</span><span class="sxs-lookup"><span data-stu-id="fa524-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fa524-741">Modifiez les paramètres sur cette valeur par `HttpMessageHandler` défaut et retournez-la, soit retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="fa524-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fa524-742">**Lorsque vous remplacez le gestionnaire assurez-vous de copier les paramètres que vous souhaitez conserver du gestionnaire fourni, sinon, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="fa524-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fa524-743">Un proxy HTTP à utiliser lors de l’envoi de demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fa524-744">Réglez ce boolean pour envoyer les informations d’identification par défaut pour les demandes HTTP et WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fa524-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fa524-745">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="fa524-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fa524-746">Un délégué qui peut être utilisé pour configurer d’autres options WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fa524-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fa524-747">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peuvent être utilisées pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="fa524-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="fa524-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fa524-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fa524-749">JavaScript Option</span><span class="sxs-lookup"><span data-stu-id="fa524-749">JavaScript Option</span></span> | <span data-ttu-id="fa524-750">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-750">Default Value</span></span> | <span data-ttu-id="fa524-751">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fa524-752">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fa524-753">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-754">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-755">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="fa524-756">Java</span><span class="sxs-lookup"><span data-stu-id="fa524-756">Java</span></span>](#tab/java)

| <span data-ttu-id="fa524-757">Java Option</span><span class="sxs-lookup"><span data-stu-id="fa524-757">Java Option</span></span> | <span data-ttu-id="fa524-758">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="fa524-758">Default Value</span></span> | <span data-ttu-id="fa524-759">Description</span><span class="sxs-lookup"><span data-stu-id="fa524-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fa524-760">Une fonction de retour d’une chaîne qui est fournie comme un jeton d’authentification Bearer dans les demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fa524-761">Réglez `true` ceci pour sauter l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="fa524-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fa524-762">**Seulement pris en charge lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="fa524-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fa524-763">Ce paramètre ne peut pas être activé lors de l’utilisation du service SignalR Azure.</span><span class="sxs-lookup"><span data-stu-id="fa524-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fa524-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fa524-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fa524-765">Vide</span><span class="sxs-lookup"><span data-stu-id="fa524-765">Empty</span></span> | <span data-ttu-id="fa524-766">Une carte d’en-têtes HTTP supplémentaires à envoyer à chaque demande HTTP.</span><span class="sxs-lookup"><span data-stu-id="fa524-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fa524-767">Dans le client .NET, ces options peuvent être `WithUrl`modifiées par les options que le délégué a fournies à :</span><span class="sxs-lookup"><span data-stu-id="fa524-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fa524-768">Dans le client JavaScript, ces options peuvent être `withUrl`fournies dans un objet JavaScript fourni à :</span><span class="sxs-lookup"><span data-stu-id="fa524-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fa524-769">Dans le client Java, ces options peuvent être `HttpHubConnectionBuilder` configurées avec les méthodes`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="fa524-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fa524-770">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fa524-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
