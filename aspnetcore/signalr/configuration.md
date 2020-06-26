---
title: Configuration de ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment configurer des SignalR applications ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406834"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="048b9-103">Configuration de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="048b9-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="048b9-104">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="048b9-105">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="048b9-106">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="048b9-107">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="048b9-108">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="048b9-109">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="048b9-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="048b9-110">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="048b9-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="048b9-111">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="048b9-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="048b9-112">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="048b9-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="048b9-113">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="048b9-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="048b9-114">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="048b9-115">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="048b9-116">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="048b9-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="048b9-117">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="048b9-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="048b9-118">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-118">MessagePack serialization options</span></span>

<span data-ttu-id="048b9-119">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="048b9-120">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-121">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="048b9-122">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="048b9-122">Configure server options</span></span>

<span data-ttu-id="048b9-123">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="048b9-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="048b9-124">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-124">Option</span></span> | <span data-ttu-id="048b9-125">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-125">Default Value</span></span> | <span data-ttu-id="048b9-126">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="048b9-127">30 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-127">30 seconds</span></span> | <span data-ttu-id="048b9-128">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="048b9-129">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="048b9-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="048b9-130">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="048b9-131">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-131">15 seconds</span></span> | <span data-ttu-id="048b9-132">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="048b9-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="048b9-133">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-134">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-135">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-135">15 seconds</span></span> | <span data-ttu-id="048b9-136">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="048b9-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="048b9-137">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="048b9-138">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="048b9-139">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="048b9-139">All installed protocols</span></span> | <span data-ttu-id="048b9-140">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-140">Protocols supported by this hub.</span></span> <span data-ttu-id="048b9-141">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="048b9-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="048b9-142">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="048b9-143">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="048b9-144">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="048b9-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="048b9-145">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="048b9-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="048b9-146">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-146">32 KB</span></span> | <span data-ttu-id="048b9-147">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="048b9-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="048b9-148">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="048b9-149">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="048b9-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="048b9-150">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="048b9-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="048b9-151">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="048b9-152">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="048b9-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="048b9-153">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="048b9-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="048b9-154">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-154">Option</span></span> | <span data-ttu-id="048b9-155">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-155">Default Value</span></span> | <span data-ttu-id="048b9-156">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="048b9-157">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-157">32 KB</span></span> | <span data-ttu-id="048b9-158">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="048b9-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="048b9-159">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="048b9-160">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="048b9-161">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="048b9-162">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-162">32 KB</span></span> | <span data-ttu-id="048b9-163">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="048b9-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="048b9-164">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="048b9-165">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="048b9-165">All Transports are enabled.</span></span> | <span data-ttu-id="048b9-166">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="048b9-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="048b9-167">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-167">See below.</span></span> | <span data-ttu-id="048b9-168">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="048b9-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="048b9-169">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-169">See below.</span></span> | <span data-ttu-id="048b9-170">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="048b9-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="048b9-171">0</span><span class="sxs-lookup"><span data-stu-id="048b9-171">0</span></span> | <span data-ttu-id="048b9-172">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="048b9-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="048b9-173">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="048b9-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="048b9-174">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="048b9-175">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-175">Option</span></span> | <span data-ttu-id="048b9-176">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-176">Default Value</span></span> | <span data-ttu-id="048b9-177">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="048b9-178">90 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-178">90 seconds</span></span> | <span data-ttu-id="048b9-179">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="048b9-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="048b9-180">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="048b9-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="048b9-181">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="048b9-182">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-182">Option</span></span> | <span data-ttu-id="048b9-183">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-183">Default Value</span></span> | <span data-ttu-id="048b9-184">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="048b9-185">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-185">5 seconds</span></span> | <span data-ttu-id="048b9-186">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="048b9-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="048b9-187">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="048b9-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="048b9-188">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="048b9-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="048b9-189">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="048b9-189">Configure client options</span></span>

<span data-ttu-id="048b9-190">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="048b9-191">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="048b9-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="048b9-192">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="048b9-192">Configure logging</span></span>

<span data-ttu-id="048b9-193">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="048b9-194">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="048b9-195">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="048b9-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-196">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="048b9-197">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="048b9-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="048b9-198">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="048b9-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="048b9-199">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="048b9-200">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="048b9-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="048b9-201">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="048b9-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="048b9-202">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="048b9-203">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="048b9-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="048b9-204">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="048b9-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="048b9-205">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-205">The following table lists the available log levels.</span></span> <span data-ttu-id="048b9-206">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="048b9-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="048b9-207">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="048b9-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="048b9-208">String</span><span class="sxs-lookup"><span data-stu-id="048b9-208">String</span></span>                      | <span data-ttu-id="048b9-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="048b9-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="048b9-210">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="048b9-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="048b9-211">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="048b9-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="048b9-212">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="048b9-213">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="048b9-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="048b9-214">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="048b9-215">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="048b9-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="048b9-216">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="048b9-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="048b9-217">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="048b9-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="048b9-218">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="048b9-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="048b9-219">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="048b9-219">Configure allowed transports</span></span>

<span data-ttu-id="048b9-220">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="048b9-221">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="048b9-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="048b9-222">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-222">All transports are enabled by default.</span></span>

<span data-ttu-id="048b9-223">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="048b9-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="048b9-224">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="048b9-225">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="048b9-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="048b9-226">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="048b9-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="048b9-227">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="048b9-228">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="048b9-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="048b9-229">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="048b9-229">Configure bearer authentication</span></span>

<span data-ttu-id="048b9-230">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="048b9-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="048b9-231">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="048b9-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="048b9-232">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="048b9-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="048b9-233">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="048b9-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="048b9-234">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="048b9-235">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="048b9-236">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="048b9-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="048b9-237">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="048b9-238">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="048b9-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="048b9-239">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="048b9-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="048b9-240">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="048b9-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-241">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-242">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-242">Option</span></span> | <span data-ttu-id="048b9-243">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-243">Default value</span></span> | <span data-ttu-id="048b9-244">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="048b9-245">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-246">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-246">Timeout for server activity.</span></span> <span data-ttu-id="048b9-247">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-248">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-249">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="048b9-250">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-250">15 seconds</span></span> | <span data-ttu-id="048b9-251">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-252">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-253">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-254">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-255">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-255">15 seconds</span></span> | <span data-ttu-id="048b9-256">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-257">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-258">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="048b9-259">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="048b9-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="048b9-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-261">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-261">Option</span></span> | <span data-ttu-id="048b9-262">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-262">Default value</span></span> | <span data-ttu-id="048b9-263">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="048b9-264">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-265">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-265">Timeout for server activity.</span></span> <span data-ttu-id="048b9-266">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="048b9-267">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-268">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="048b9-269">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-270">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-271">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-272">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-273">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-273">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-274">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-274">Option</span></span> | <span data-ttu-id="048b9-275">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-275">Default value</span></span> | <span data-ttu-id="048b9-276">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="048b9-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="048b9-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="048b9-278">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-279">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-279">Timeout for server activity.</span></span> <span data-ttu-id="048b9-280">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-281">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-282">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="048b9-283">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-283">15 seconds</span></span> | <span data-ttu-id="048b9-284">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-285">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-286">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-287">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="048b9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="048b9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="048b9-289">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-290">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-291">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-292">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="048b9-293">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-293">Configure additional options</span></span>

<span data-ttu-id="048b9-294">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="048b9-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-295">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-296">Option .NET</span><span class="sxs-lookup"><span data-stu-id="048b9-296">.NET Option</span></span> |  <span data-ttu-id="048b9-297">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-297">Default value</span></span> | <span data-ttu-id="048b9-298">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="048b9-299">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="048b9-300">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-301">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-302">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="048b9-303">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-303">Empty</span></span> | <span data-ttu-id="048b9-304">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="048b9-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="048b9-305">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-305">Empty</span></span> | <span data-ttu-id="048b9-306">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="048b9-307">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-307">Empty</span></span> | <span data-ttu-id="048b9-308">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="048b9-309">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-309">5 seconds</span></span> | <span data-ttu-id="048b9-310">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="048b9-310">WebSockets only.</span></span> <span data-ttu-id="048b9-311">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="048b9-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="048b9-312">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="048b9-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="048b9-313">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-313">Empty</span></span> | <span data-ttu-id="048b9-314">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="048b9-315">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="048b9-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="048b9-316">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="048b9-317">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="048b9-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="048b9-318">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="048b9-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="048b9-319">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="048b9-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="048b9-320">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="048b9-321">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="048b9-322">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="048b9-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="048b9-323">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="048b9-324">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="048b9-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="048b9-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-326">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-326">JavaScript Option</span></span> | <span data-ttu-id="048b9-327">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-327">Default Value</span></span> | <span data-ttu-id="048b9-328">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="048b9-329">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="048b9-330">Dictionnaire d’en-têtes envoyés avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="048b9-331">L’envoi d’en-têtes dans le navigateur ne fonctionne pas pour les WebSockets ou le <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flux.</span><span class="sxs-lookup"><span data-stu-id="048b9-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="048b9-332">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="048b9-333">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-334">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-335">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="048b9-336">Spécifie si les informations d’identification seront envoyées avec la demande CORS.</span><span class="sxs-lookup"><span data-stu-id="048b9-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="048b9-337">Azure App Service utilise des cookies pour les sessions rémanentes et a besoin que cette option soit activée pour fonctionner correctement.</span><span class="sxs-lookup"><span data-stu-id="048b9-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="048b9-338">Pour plus d’informations sur CORS avec SignalR , consultez <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="048b9-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-339">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-339">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-340">Option Java</span><span class="sxs-lookup"><span data-stu-id="048b9-340">Java Option</span></span> | <span data-ttu-id="048b9-341">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-341">Default Value</span></span> | <span data-ttu-id="048b9-342">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="048b9-343">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="048b9-344">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-345">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-346">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="048b9-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="048b9-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="048b9-348">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-348">Empty</span></span> | <span data-ttu-id="048b9-349">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="048b9-350">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="048b9-351">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="048b9-352">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="048b9-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="048b9-353">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="048b9-354">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="048b9-355">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="048b9-356">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="048b9-357">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="048b9-358">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="048b9-359">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="048b9-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="048b9-360">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="048b9-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="048b9-361">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="048b9-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="048b9-362">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="048b9-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="048b9-363">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="048b9-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="048b9-364">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="048b9-365">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="048b9-366">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="048b9-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="048b9-367">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="048b9-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="048b9-368">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-368">MessagePack serialization options</span></span>

<span data-ttu-id="048b9-369">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="048b9-370">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-371">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="048b9-372">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="048b9-372">Configure server options</span></span>

<span data-ttu-id="048b9-373">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="048b9-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="048b9-374">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-374">Option</span></span> | <span data-ttu-id="048b9-375">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-375">Default Value</span></span> | <span data-ttu-id="048b9-376">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="048b9-377">30 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-377">30 seconds</span></span> | <span data-ttu-id="048b9-378">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="048b9-379">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="048b9-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="048b9-380">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="048b9-381">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-381">15 seconds</span></span> | <span data-ttu-id="048b9-382">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="048b9-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="048b9-383">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-384">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-385">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-385">15 seconds</span></span> | <span data-ttu-id="048b9-386">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="048b9-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="048b9-387">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="048b9-388">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="048b9-389">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="048b9-389">All installed protocols</span></span> | <span data-ttu-id="048b9-390">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-390">Protocols supported by this hub.</span></span> <span data-ttu-id="048b9-391">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="048b9-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="048b9-392">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="048b9-393">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="048b9-394">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="048b9-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="048b9-395">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="048b9-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="048b9-396">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-396">32 KB</span></span> | <span data-ttu-id="048b9-397">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="048b9-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="048b9-398">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="048b9-399">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="048b9-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="048b9-400">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="048b9-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="048b9-401">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="048b9-402">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="048b9-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="048b9-403">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="048b9-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="048b9-404">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-404">Option</span></span> | <span data-ttu-id="048b9-405">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-405">Default Value</span></span> | <span data-ttu-id="048b9-406">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="048b9-407">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-407">32 KB</span></span> | <span data-ttu-id="048b9-408">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="048b9-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="048b9-409">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="048b9-410">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="048b9-411">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="048b9-412">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-412">32 KB</span></span> | <span data-ttu-id="048b9-413">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="048b9-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="048b9-414">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="048b9-415">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="048b9-415">All Transports are enabled.</span></span> | <span data-ttu-id="048b9-416">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="048b9-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="048b9-417">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-417">See below.</span></span> | <span data-ttu-id="048b9-418">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="048b9-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="048b9-419">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-419">See below.</span></span> | <span data-ttu-id="048b9-420">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="048b9-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="048b9-421">0</span><span class="sxs-lookup"><span data-stu-id="048b9-421">0</span></span> | <span data-ttu-id="048b9-422">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="048b9-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="048b9-423">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="048b9-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="048b9-424">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="048b9-425">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-425">Option</span></span> | <span data-ttu-id="048b9-426">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-426">Default Value</span></span> | <span data-ttu-id="048b9-427">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="048b9-428">90 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-428">90 seconds</span></span> | <span data-ttu-id="048b9-429">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="048b9-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="048b9-430">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="048b9-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="048b9-431">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="048b9-432">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-432">Option</span></span> | <span data-ttu-id="048b9-433">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-433">Default Value</span></span> | <span data-ttu-id="048b9-434">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="048b9-435">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-435">5 seconds</span></span> | <span data-ttu-id="048b9-436">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="048b9-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="048b9-437">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="048b9-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="048b9-438">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="048b9-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="048b9-439">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="048b9-439">Configure client options</span></span>

<span data-ttu-id="048b9-440">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="048b9-441">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="048b9-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="048b9-442">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="048b9-442">Configure logging</span></span>

<span data-ttu-id="048b9-443">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="048b9-444">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="048b9-445">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="048b9-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-446">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="048b9-447">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="048b9-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="048b9-448">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="048b9-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="048b9-449">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="048b9-450">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="048b9-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="048b9-451">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="048b9-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="048b9-452">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="048b9-453">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="048b9-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="048b9-454">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="048b9-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="048b9-455">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-455">The following table lists the available log levels.</span></span> <span data-ttu-id="048b9-456">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="048b9-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="048b9-457">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="048b9-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="048b9-458">String</span><span class="sxs-lookup"><span data-stu-id="048b9-458">String</span></span>                      | <span data-ttu-id="048b9-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="048b9-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="048b9-460">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="048b9-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="048b9-461">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="048b9-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="048b9-462">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="048b9-463">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="048b9-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="048b9-464">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="048b9-465">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="048b9-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="048b9-466">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="048b9-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="048b9-467">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="048b9-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="048b9-468">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="048b9-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="048b9-469">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="048b9-469">Configure allowed transports</span></span>

<span data-ttu-id="048b9-470">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="048b9-471">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="048b9-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="048b9-472">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-472">All transports are enabled by default.</span></span>

<span data-ttu-id="048b9-473">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="048b9-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="048b9-474">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="048b9-475">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="048b9-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="048b9-476">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="048b9-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="048b9-477">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="048b9-478">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="048b9-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="048b9-479">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="048b9-479">Configure bearer authentication</span></span>

<span data-ttu-id="048b9-480">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="048b9-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="048b9-481">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="048b9-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="048b9-482">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="048b9-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="048b9-483">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="048b9-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="048b9-484">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="048b9-485">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="048b9-486">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="048b9-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="048b9-487">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="048b9-488">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="048b9-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="048b9-489">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="048b9-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="048b9-490">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="048b9-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-491">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-492">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-492">Option</span></span> | <span data-ttu-id="048b9-493">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-493">Default value</span></span> | <span data-ttu-id="048b9-494">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="048b9-495">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-496">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-496">Timeout for server activity.</span></span> <span data-ttu-id="048b9-497">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-498">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-499">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="048b9-500">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-500">15 seconds</span></span> | <span data-ttu-id="048b9-501">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-502">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-503">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-504">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-505">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-505">15 seconds</span></span> | <span data-ttu-id="048b9-506">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-507">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-508">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="048b9-509">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="048b9-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="048b9-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-511">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-511">Option</span></span> | <span data-ttu-id="048b9-512">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-512">Default value</span></span> | <span data-ttu-id="048b9-513">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="048b9-514">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-515">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-515">Timeout for server activity.</span></span> <span data-ttu-id="048b9-516">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="048b9-517">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-518">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="048b9-519">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-520">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-521">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-522">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-523">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-523">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-524">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-524">Option</span></span> | <span data-ttu-id="048b9-525">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-525">Default value</span></span> | <span data-ttu-id="048b9-526">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="048b9-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="048b9-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="048b9-528">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-529">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-529">Timeout for server activity.</span></span> <span data-ttu-id="048b9-530">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-531">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-532">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="048b9-533">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-533">15 seconds</span></span> | <span data-ttu-id="048b9-534">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-535">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-536">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-537">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="048b9-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="048b9-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="048b9-539">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-540">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-541">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-542">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="048b9-543">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-543">Configure additional options</span></span>

<span data-ttu-id="048b9-544">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="048b9-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-545">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-546">Option .NET</span><span class="sxs-lookup"><span data-stu-id="048b9-546">.NET Option</span></span> |  <span data-ttu-id="048b9-547">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-547">Default value</span></span> | <span data-ttu-id="048b9-548">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="048b9-549">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="048b9-550">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-551">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-552">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="048b9-553">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-553">Empty</span></span> | <span data-ttu-id="048b9-554">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="048b9-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="048b9-555">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-555">Empty</span></span> | <span data-ttu-id="048b9-556">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="048b9-557">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-557">Empty</span></span> | <span data-ttu-id="048b9-558">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="048b9-559">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-559">5 seconds</span></span> | <span data-ttu-id="048b9-560">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="048b9-560">WebSockets only.</span></span> <span data-ttu-id="048b9-561">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="048b9-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="048b9-562">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="048b9-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="048b9-563">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-563">Empty</span></span> | <span data-ttu-id="048b9-564">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="048b9-565">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="048b9-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="048b9-566">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="048b9-567">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="048b9-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="048b9-568">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="048b9-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="048b9-569">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="048b9-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="048b9-570">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="048b9-571">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="048b9-572">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="048b9-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="048b9-573">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="048b9-574">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="048b9-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="048b9-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-576">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-576">JavaScript Option</span></span> | <span data-ttu-id="048b9-577">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-577">Default Value</span></span> | <span data-ttu-id="048b9-578">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="048b9-579">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="048b9-580">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="048b9-581">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-582">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-583">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-584">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-584">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-585">Option Java</span><span class="sxs-lookup"><span data-stu-id="048b9-585">Java Option</span></span> | <span data-ttu-id="048b9-586">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-586">Default Value</span></span> | <span data-ttu-id="048b9-587">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="048b9-588">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="048b9-589">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-590">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-591">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="048b9-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="048b9-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="048b9-593">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-593">Empty</span></span> | <span data-ttu-id="048b9-594">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="048b9-595">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="048b9-596">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="048b9-597">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="048b9-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="048b9-598">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="048b9-599">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="048b9-600">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="048b9-601">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="048b9-602">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="048b9-603">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="048b9-604">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="048b9-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="048b9-605">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="048b9-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="048b9-606">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="048b9-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="048b9-607">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="048b9-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="048b9-608">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="048b9-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="048b9-609">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="048b9-610">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="048b9-611">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="048b9-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="048b9-612">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="048b9-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="048b9-613">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-613">MessagePack serialization options</span></span>

<span data-ttu-id="048b9-614">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="048b9-615">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-616">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="048b9-617">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="048b9-617">Configure server options</span></span>

<span data-ttu-id="048b9-618">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="048b9-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="048b9-619">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-619">Option</span></span> | <span data-ttu-id="048b9-620">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-620">Default Value</span></span> | <span data-ttu-id="048b9-621">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="048b9-622">30 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-622">30 seconds</span></span> | <span data-ttu-id="048b9-623">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="048b9-624">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="048b9-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="048b9-625">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="048b9-626">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-626">15 seconds</span></span> | <span data-ttu-id="048b9-627">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="048b9-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="048b9-628">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-629">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-630">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-630">15 seconds</span></span> | <span data-ttu-id="048b9-631">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="048b9-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="048b9-632">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="048b9-633">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="048b9-634">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="048b9-634">All installed protocols</span></span> | <span data-ttu-id="048b9-635">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-635">Protocols supported by this hub.</span></span> <span data-ttu-id="048b9-636">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="048b9-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="048b9-637">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="048b9-638">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="048b9-639">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="048b9-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="048b9-640">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="048b9-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="048b9-641">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-641">32 KB</span></span> | <span data-ttu-id="048b9-642">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="048b9-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="048b9-643">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="048b9-644">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="048b9-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="048b9-645">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="048b9-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="048b9-646">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="048b9-647">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="048b9-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="048b9-648">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="048b9-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="048b9-649">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-649">Option</span></span> | <span data-ttu-id="048b9-650">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-650">Default Value</span></span> | <span data-ttu-id="048b9-651">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="048b9-652">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-652">32 KB</span></span> | <span data-ttu-id="048b9-653">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="048b9-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="048b9-654">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="048b9-655">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="048b9-656">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="048b9-657">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-657">32 KB</span></span> | <span data-ttu-id="048b9-658">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="048b9-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="048b9-659">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="048b9-660">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="048b9-660">All Transports are enabled.</span></span> | <span data-ttu-id="048b9-661">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="048b9-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="048b9-662">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-662">See below.</span></span> | <span data-ttu-id="048b9-663">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="048b9-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="048b9-664">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-664">See below.</span></span> | <span data-ttu-id="048b9-665">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="048b9-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="048b9-666">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="048b9-667">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-667">Option</span></span> | <span data-ttu-id="048b9-668">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-668">Default Value</span></span> | <span data-ttu-id="048b9-669">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="048b9-670">90 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-670">90 seconds</span></span> | <span data-ttu-id="048b9-671">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="048b9-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="048b9-672">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="048b9-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="048b9-673">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="048b9-674">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-674">Option</span></span> | <span data-ttu-id="048b9-675">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-675">Default Value</span></span> | <span data-ttu-id="048b9-676">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="048b9-677">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-677">5 seconds</span></span> | <span data-ttu-id="048b9-678">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="048b9-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="048b9-679">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="048b9-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="048b9-680">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="048b9-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="048b9-681">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="048b9-681">Configure client options</span></span>

<span data-ttu-id="048b9-682">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="048b9-683">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="048b9-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="048b9-684">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="048b9-684">Configure logging</span></span>

<span data-ttu-id="048b9-685">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="048b9-686">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="048b9-687">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="048b9-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-688">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="048b9-689">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="048b9-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="048b9-690">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="048b9-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="048b9-691">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="048b9-692">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="048b9-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="048b9-693">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="048b9-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="048b9-694">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="048b9-695">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="048b9-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="048b9-696">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="048b9-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="048b9-697">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-697">The following table lists the available log levels.</span></span> <span data-ttu-id="048b9-698">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="048b9-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="048b9-699">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="048b9-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="048b9-700">String</span><span class="sxs-lookup"><span data-stu-id="048b9-700">String</span></span>                      | <span data-ttu-id="048b9-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="048b9-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="048b9-702">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="048b9-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="048b9-703">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="048b9-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="048b9-704">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="048b9-705">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="048b9-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="048b9-706">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="048b9-707">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="048b9-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="048b9-708">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="048b9-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="048b9-709">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="048b9-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="048b9-710">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="048b9-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="048b9-711">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="048b9-711">Configure allowed transports</span></span>

<span data-ttu-id="048b9-712">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="048b9-713">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="048b9-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="048b9-714">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-714">All transports are enabled by default.</span></span>

<span data-ttu-id="048b9-715">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="048b9-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="048b9-716">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="048b9-717">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="048b9-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="048b9-718">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="048b9-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="048b9-719">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="048b9-720">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="048b9-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="048b9-721">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="048b9-721">Configure bearer authentication</span></span>

<span data-ttu-id="048b9-722">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="048b9-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="048b9-723">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="048b9-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="048b9-724">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="048b9-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="048b9-725">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="048b9-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="048b9-726">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="048b9-727">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="048b9-728">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="048b9-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="048b9-729">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="048b9-730">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="048b9-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="048b9-731">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="048b9-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="048b9-732">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="048b9-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-733">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-734">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-734">Option</span></span> | <span data-ttu-id="048b9-735">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-735">Default value</span></span> | <span data-ttu-id="048b9-736">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="048b9-737">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-738">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-738">Timeout for server activity.</span></span> <span data-ttu-id="048b9-739">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-740">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-741">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="048b9-742">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-742">15 seconds</span></span> | <span data-ttu-id="048b9-743">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-744">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-745">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-746">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-747">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-747">15 seconds</span></span> | <span data-ttu-id="048b9-748">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-749">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-750">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="048b9-751">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="048b9-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="048b9-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-753">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-753">Option</span></span> | <span data-ttu-id="048b9-754">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-754">Default value</span></span> | <span data-ttu-id="048b9-755">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="048b9-756">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-757">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-757">Timeout for server activity.</span></span> <span data-ttu-id="048b9-758">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="048b9-759">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-760">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="048b9-761">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-762">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-763">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-764">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-765">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-765">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-766">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-766">Option</span></span> | <span data-ttu-id="048b9-767">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-767">Default value</span></span> | <span data-ttu-id="048b9-768">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="048b9-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="048b9-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="048b9-770">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-771">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-771">Timeout for server activity.</span></span> <span data-ttu-id="048b9-772">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-773">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-774">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="048b9-775">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-775">15 seconds</span></span> | <span data-ttu-id="048b9-776">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-777">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-778">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-779">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="048b9-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="048b9-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="048b9-781">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-782">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-783">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-784">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="048b9-785">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-785">Configure additional options</span></span>

<span data-ttu-id="048b9-786">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="048b9-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-787">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-788">Option .NET</span><span class="sxs-lookup"><span data-stu-id="048b9-788">.NET Option</span></span> |  <span data-ttu-id="048b9-789">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-789">Default value</span></span> | <span data-ttu-id="048b9-790">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="048b9-791">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="048b9-792">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-793">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-794">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="048b9-795">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-795">Empty</span></span> | <span data-ttu-id="048b9-796">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="048b9-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="048b9-797">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-797">Empty</span></span> | <span data-ttu-id="048b9-798">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="048b9-799">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-799">Empty</span></span> | <span data-ttu-id="048b9-800">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="048b9-801">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-801">5 seconds</span></span> | <span data-ttu-id="048b9-802">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="048b9-802">WebSockets only.</span></span> <span data-ttu-id="048b9-803">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="048b9-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="048b9-804">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="048b9-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="048b9-805">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-805">Empty</span></span> | <span data-ttu-id="048b9-806">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="048b9-807">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="048b9-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="048b9-808">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="048b9-809">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="048b9-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="048b9-810">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="048b9-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="048b9-811">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="048b9-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="048b9-812">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="048b9-813">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="048b9-814">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="048b9-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="048b9-815">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="048b9-816">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="048b9-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="048b9-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-818">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-818">JavaScript Option</span></span> | <span data-ttu-id="048b9-819">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-819">Default Value</span></span> | <span data-ttu-id="048b9-820">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="048b9-821">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="048b9-822">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="048b9-823">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-824">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-825">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-826">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-826">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-827">Option Java</span><span class="sxs-lookup"><span data-stu-id="048b9-827">Java Option</span></span> | <span data-ttu-id="048b9-828">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-828">Default Value</span></span> | <span data-ttu-id="048b9-829">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="048b9-830">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="048b9-831">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-832">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-833">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="048b9-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="048b9-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="048b9-835">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-835">Empty</span></span> | <span data-ttu-id="048b9-836">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="048b9-837">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="048b9-838">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="048b9-839">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="048b9-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="048b9-840">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="048b9-841">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="048b9-842">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="048b9-843">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="048b9-844">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="048b9-845">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="048b9-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="048b9-846">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un `JsonSerializerSettings` objet JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="048b9-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="048b9-847">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="048b9-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="048b9-848">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="048b9-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="048b9-849">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="048b9-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="048b9-850">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="048b9-851">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="048b9-852">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-852">MessagePack serialization options</span></span>

<span data-ttu-id="048b9-853">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="048b9-854">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-855">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="048b9-856">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="048b9-856">Configure server options</span></span>

<span data-ttu-id="048b9-857">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="048b9-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="048b9-858">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-858">Option</span></span> | <span data-ttu-id="048b9-859">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-859">Default Value</span></span> | <span data-ttu-id="048b9-860">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="048b9-861">30 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-861">30 seconds</span></span> | <span data-ttu-id="048b9-862">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="048b9-863">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="048b9-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="048b9-864">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="048b9-865">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-865">15 seconds</span></span> | <span data-ttu-id="048b9-866">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="048b9-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="048b9-867">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-868">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-869">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-869">15 seconds</span></span> | <span data-ttu-id="048b9-870">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="048b9-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="048b9-871">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="048b9-872">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="048b9-873">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="048b9-873">All installed protocols</span></span> | <span data-ttu-id="048b9-874">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-874">Protocols supported by this hub.</span></span> <span data-ttu-id="048b9-875">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="048b9-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="048b9-876">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="048b9-877">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="048b9-878">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="048b9-879">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="048b9-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="048b9-880">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="048b9-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="048b9-881">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="048b9-882">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="048b9-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="048b9-883">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="048b9-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="048b9-884">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-884">Option</span></span> | <span data-ttu-id="048b9-885">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-885">Default Value</span></span> | <span data-ttu-id="048b9-886">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="048b9-887">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-887">32 KB</span></span> | <span data-ttu-id="048b9-888">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="048b9-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="048b9-889">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="048b9-890">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="048b9-891">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="048b9-892">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-892">32 KB</span></span> | <span data-ttu-id="048b9-893">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="048b9-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="048b9-894">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="048b9-895">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="048b9-895">All Transports are enabled.</span></span> | <span data-ttu-id="048b9-896">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="048b9-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="048b9-897">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-897">See below.</span></span> | <span data-ttu-id="048b9-898">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="048b9-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="048b9-899">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-899">See below.</span></span> | <span data-ttu-id="048b9-900">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="048b9-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="048b9-901">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="048b9-902">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-902">Option</span></span> | <span data-ttu-id="048b9-903">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-903">Default Value</span></span> | <span data-ttu-id="048b9-904">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="048b9-905">90 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-905">90 seconds</span></span> | <span data-ttu-id="048b9-906">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="048b9-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="048b9-907">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="048b9-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="048b9-908">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="048b9-909">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-909">Option</span></span> | <span data-ttu-id="048b9-910">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-910">Default Value</span></span> | <span data-ttu-id="048b9-911">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="048b9-912">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-912">5 seconds</span></span> | <span data-ttu-id="048b9-913">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="048b9-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="048b9-914">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="048b9-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="048b9-915">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="048b9-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="048b9-916">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="048b9-916">Configure client options</span></span>

<span data-ttu-id="048b9-917">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="048b9-918">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="048b9-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="048b9-919">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="048b9-919">Configure logging</span></span>

<span data-ttu-id="048b9-920">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="048b9-921">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="048b9-922">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="048b9-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-923">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="048b9-924">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="048b9-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="048b9-925">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="048b9-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="048b9-926">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="048b9-927">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="048b9-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="048b9-928">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="048b9-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="048b9-929">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="048b9-930">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="048b9-931">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="048b9-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="048b9-932">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="048b9-933">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="048b9-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="048b9-934">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="048b9-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="048b9-935">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="048b9-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="048b9-936">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="048b9-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="048b9-937">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="048b9-937">Configure allowed transports</span></span>

<span data-ttu-id="048b9-938">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="048b9-939">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="048b9-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="048b9-940">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-940">All transports are enabled by default.</span></span>

<span data-ttu-id="048b9-941">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="048b9-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="048b9-942">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="048b9-943">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="048b9-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="048b9-944">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="048b9-944">Configure bearer authentication</span></span>

<span data-ttu-id="048b9-945">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="048b9-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="048b9-946">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="048b9-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="048b9-947">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="048b9-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="048b9-948">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="048b9-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="048b9-949">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="048b9-950">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="048b9-951">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="048b9-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="048b9-952">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="048b9-953">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="048b9-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="048b9-954">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="048b9-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="048b9-955">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="048b9-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-956">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-957">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-957">Option</span></span> | <span data-ttu-id="048b9-958">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-958">Default value</span></span> | <span data-ttu-id="048b9-959">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="048b9-960">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-961">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-961">Timeout for server activity.</span></span> <span data-ttu-id="048b9-962">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-963">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-964">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="048b9-965">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-965">15 seconds</span></span> | <span data-ttu-id="048b9-966">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-967">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-968">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-969">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-970">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-970">15 seconds</span></span> | <span data-ttu-id="048b9-971">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-972">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-973">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="048b9-974">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="048b9-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="048b9-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-976">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-976">Option</span></span> | <span data-ttu-id="048b9-977">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-977">Default value</span></span> | <span data-ttu-id="048b9-978">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="048b9-979">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-980">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-980">Timeout for server activity.</span></span> <span data-ttu-id="048b9-981">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="048b9-982">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-983">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="048b9-984">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-985">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-986">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-987">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-988">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-988">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-989">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-989">Option</span></span> | <span data-ttu-id="048b9-990">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-990">Default value</span></span> | <span data-ttu-id="048b9-991">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="048b9-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="048b9-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="048b9-993">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-994">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-994">Timeout for server activity.</span></span> <span data-ttu-id="048b9-995">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-996">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-997">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="048b9-998">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-998">15 seconds</span></span> | <span data-ttu-id="048b9-999">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-1000">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-1001">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-1002">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="048b9-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="048b9-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="048b9-1004">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="048b9-1005">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="048b9-1006">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="048b9-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="048b9-1007">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="048b9-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="048b9-1008">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-1008">Configure additional options</span></span>

<span data-ttu-id="048b9-1009">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="048b9-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-1011">Option .NET</span><span class="sxs-lookup"><span data-stu-id="048b9-1011">.NET Option</span></span> |  <span data-ttu-id="048b9-1012">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1012">Default value</span></span> | <span data-ttu-id="048b9-1013">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="048b9-1014">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="048b9-1015">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-1016">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-1017">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="048b9-1018">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1018">Empty</span></span> | <span data-ttu-id="048b9-1019">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="048b9-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="048b9-1020">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1020">Empty</span></span> | <span data-ttu-id="048b9-1021">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="048b9-1022">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1022">Empty</span></span> | <span data-ttu-id="048b9-1023">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="048b9-1024">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1024">5 seconds</span></span> | <span data-ttu-id="048b9-1025">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="048b9-1025">WebSockets only.</span></span> <span data-ttu-id="048b9-1026">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="048b9-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="048b9-1027">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="048b9-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="048b9-1028">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1028">Empty</span></span> | <span data-ttu-id="048b9-1029">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="048b9-1030">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="048b9-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="048b9-1031">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="048b9-1032">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="048b9-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="048b9-1033">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="048b9-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="048b9-1034">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="048b9-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="048b9-1035">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="048b9-1036">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="048b9-1037">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="048b9-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="048b9-1038">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="048b9-1039">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="048b9-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="048b9-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-1041">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-1041">JavaScript Option</span></span> | <span data-ttu-id="048b9-1042">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1042">Default Value</span></span> | <span data-ttu-id="048b9-1043">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="048b9-1044">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="048b9-1045">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="048b9-1046">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-1047">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-1048">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-1049">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-1050">Option Java</span><span class="sxs-lookup"><span data-stu-id="048b9-1050">Java Option</span></span> | <span data-ttu-id="048b9-1051">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1051">Default Value</span></span> | <span data-ttu-id="048b9-1052">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="048b9-1053">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="048b9-1054">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-1055">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-1056">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="048b9-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="048b9-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="048b9-1058">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1058">Empty</span></span> | <span data-ttu-id="048b9-1059">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="048b9-1060">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="048b9-1061">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="048b9-1062">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="048b9-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="048b9-1063">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="048b9-1064">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="048b9-1065">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="048b9-1066">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="048b9-1067">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="048b9-1068">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="048b9-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="048b9-1069">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un `JsonSerializerSettings` objet JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="048b9-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="048b9-1070">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="048b9-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="048b9-1071">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="048b9-1072">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="048b9-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="048b9-1073">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="048b9-1074">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="048b9-1075">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="048b9-1075">MessagePack serialization options</span></span>

<span data-ttu-id="048b9-1076">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="048b9-1077">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="048b9-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-1078">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="048b9-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="048b9-1079">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="048b9-1079">Configure server options</span></span>

<span data-ttu-id="048b9-1080">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="048b9-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="048b9-1081">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-1081">Option</span></span> | <span data-ttu-id="048b9-1082">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1082">Default Value</span></span> | <span data-ttu-id="048b9-1083">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="048b9-1084">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1084">15 seconds</span></span> | <span data-ttu-id="048b9-1085">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="048b9-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="048b9-1086">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-1087">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="048b9-1088">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1088">15 seconds</span></span> | <span data-ttu-id="048b9-1089">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="048b9-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="048b9-1090">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="048b9-1091">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="048b9-1092">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="048b9-1092">All installed protocols</span></span> | <span data-ttu-id="048b9-1093">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="048b9-1094">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="048b9-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="048b9-1095">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="048b9-1096">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="048b9-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="048b9-1097">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="048b9-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="048b9-1098">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="048b9-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="048b9-1099">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="048b9-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="048b9-1100">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="048b9-1101">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="048b9-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="048b9-1102">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="048b9-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="048b9-1103">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-1103">Option</span></span> | <span data-ttu-id="048b9-1104">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1104">Default Value</span></span> | <span data-ttu-id="048b9-1105">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="048b9-1106">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-1106">32 KB</span></span> | <span data-ttu-id="048b9-1107">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="048b9-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="048b9-1108">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="048b9-1109">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="048b9-1110">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="048b9-1111">32 Ko</span><span class="sxs-lookup"><span data-stu-id="048b9-1111">32 KB</span></span> | <span data-ttu-id="048b9-1112">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="048b9-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="048b9-1113">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="048b9-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="048b9-1114">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="048b9-1114">All Transports are enabled.</span></span> | <span data-ttu-id="048b9-1115">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="048b9-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="048b9-1116">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-1116">See below.</span></span> | <span data-ttu-id="048b9-1117">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="048b9-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="048b9-1118">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="048b9-1118">See below.</span></span> | <span data-ttu-id="048b9-1119">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="048b9-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="048b9-1120">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="048b9-1121">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-1121">Option</span></span> | <span data-ttu-id="048b9-1122">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1122">Default Value</span></span> | <span data-ttu-id="048b9-1123">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="048b9-1124">90 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1124">90 seconds</span></span> | <span data-ttu-id="048b9-1125">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="048b9-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="048b9-1126">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="048b9-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="048b9-1127">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="048b9-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="048b9-1128">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-1128">Option</span></span> | <span data-ttu-id="048b9-1129">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1129">Default Value</span></span> | <span data-ttu-id="048b9-1130">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="048b9-1131">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1131">5 seconds</span></span> | <span data-ttu-id="048b9-1132">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="048b9-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="048b9-1133">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="048b9-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="048b9-1134">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="048b9-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="048b9-1135">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="048b9-1135">Configure client options</span></span>

<span data-ttu-id="048b9-1136">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="048b9-1137">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="048b9-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="048b9-1138">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="048b9-1138">Configure logging</span></span>

<span data-ttu-id="048b9-1139">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="048b9-1140">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="048b9-1141">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="048b9-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="048b9-1142">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="048b9-1143">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="048b9-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="048b9-1144">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="048b9-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="048b9-1145">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="048b9-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="048b9-1146">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="048b9-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="048b9-1147">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="048b9-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="048b9-1148">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="048b9-1149">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="048b9-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="048b9-1150">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="048b9-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="048b9-1151">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="048b9-1152">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="048b9-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="048b9-1153">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="048b9-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="048b9-1154">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="048b9-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="048b9-1155">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="048b9-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="048b9-1156">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="048b9-1156">Configure allowed transports</span></span>

<span data-ttu-id="048b9-1157">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="048b9-1158">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="048b9-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="048b9-1159">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="048b9-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="048b9-1160">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="048b9-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="048b9-1161">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="048b9-1162">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="048b9-1162">Configure bearer authentication</span></span>

<span data-ttu-id="048b9-1163">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="048b9-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="048b9-1164">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="048b9-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="048b9-1165">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="048b9-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="048b9-1166">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="048b9-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="048b9-1167">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="048b9-1168">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="048b9-1169">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="048b9-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="048b9-1170">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="048b9-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="048b9-1171">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="048b9-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="048b9-1172">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="048b9-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="048b9-1173">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="048b9-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-1175">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-1175">Option</span></span> | <span data-ttu-id="048b9-1176">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1176">Default value</span></span> | <span data-ttu-id="048b9-1177">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="048b9-1178">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-1179">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1179">Timeout for server activity.</span></span> <span data-ttu-id="048b9-1180">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-1181">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-1182">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="048b9-1183">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1183">15 seconds</span></span> | <span data-ttu-id="048b9-1184">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-1185">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="048b9-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="048b9-1186">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-1187">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="048b9-1188">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="048b9-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="048b9-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-1190">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-1190">Option</span></span> | <span data-ttu-id="048b9-1191">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1191">Default value</span></span> | <span data-ttu-id="048b9-1192">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="048b9-1193">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-1194">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1194">Timeout for server activity.</span></span> <span data-ttu-id="048b9-1195">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="048b9-1196">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-1197">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-1198">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-1199">Option</span><span class="sxs-lookup"><span data-stu-id="048b9-1199">Option</span></span> | <span data-ttu-id="048b9-1200">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1200">Default value</span></span> | <span data-ttu-id="048b9-1201">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="048b9-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="048b9-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="048b9-1203">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="048b9-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="048b9-1204">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1204">Timeout for server activity.</span></span> <span data-ttu-id="048b9-1205">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-1206">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="048b9-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="048b9-1207">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` , pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="048b9-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="048b9-1208">15 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1208">15 seconds</span></span> | <span data-ttu-id="048b9-1209">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="048b9-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="048b9-1210">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="048b9-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="048b9-1211">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="048b9-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="048b9-1212">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="048b9-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="048b9-1213">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-1213">Configure additional options</span></span>

<span data-ttu-id="048b9-1214">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="048b9-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="048b9-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="048b9-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="048b9-1216">Option .NET</span><span class="sxs-lookup"><span data-stu-id="048b9-1216">.NET Option</span></span> |  <span data-ttu-id="048b9-1217">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1217">Default value</span></span> | <span data-ttu-id="048b9-1218">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="048b9-1219">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="048b9-1220">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-1221">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-1222">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="048b9-1223">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1223">Empty</span></span> | <span data-ttu-id="048b9-1224">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="048b9-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="048b9-1225">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1225">Empty</span></span> | <span data-ttu-id="048b9-1226">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="048b9-1227">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1227">Empty</span></span> | <span data-ttu-id="048b9-1228">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="048b9-1229">5 secondes</span><span class="sxs-lookup"><span data-stu-id="048b9-1229">5 seconds</span></span> | <span data-ttu-id="048b9-1230">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="048b9-1230">WebSockets only.</span></span> <span data-ttu-id="048b9-1231">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="048b9-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="048b9-1232">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="048b9-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="048b9-1233">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1233">Empty</span></span> | <span data-ttu-id="048b9-1234">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="048b9-1235">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="048b9-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="048b9-1236">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="048b9-1237">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="048b9-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="048b9-1238">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="048b9-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="048b9-1239">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="048b9-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="048b9-1240">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="048b9-1241">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="048b9-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="048b9-1242">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="048b9-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="048b9-1243">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="048b9-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="048b9-1244">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="048b9-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="048b9-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="048b9-1246">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="048b9-1246">JavaScript Option</span></span> | <span data-ttu-id="048b9-1247">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1247">Default Value</span></span> | <span data-ttu-id="048b9-1248">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="048b9-1249">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="048b9-1250">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="048b9-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="048b9-1251">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-1252">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-1253">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="048b9-1254">Java</span><span class="sxs-lookup"><span data-stu-id="048b9-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="048b9-1255">Option Java</span><span class="sxs-lookup"><span data-stu-id="048b9-1255">Java Option</span></span> | <span data-ttu-id="048b9-1256">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="048b9-1256">Default Value</span></span> | <span data-ttu-id="048b9-1257">Description</span><span class="sxs-lookup"><span data-stu-id="048b9-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="048b9-1258">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="048b9-1259">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="048b9-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="048b9-1260">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="048b9-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="048b9-1261">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="048b9-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="048b9-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="048b9-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="048b9-1263">Vide</span><span class="sxs-lookup"><span data-stu-id="048b9-1263">Empty</span></span> | <span data-ttu-id="048b9-1264">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="048b9-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="048b9-1265">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="048b9-1266">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="048b9-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="048b9-1267">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="048b9-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="048b9-1268">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="048b9-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
