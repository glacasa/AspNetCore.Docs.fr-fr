---
title: Configuration de ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment configurer des SignalR applications ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393871"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="eb027-103">Configuration de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="eb027-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eb027-104">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eb027-105">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eb027-106">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eb027-107">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="eb027-108">`AddJsonProtocol`peut être ajouté après [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb027-109">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="eb027-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eb027-110">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="eb027-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eb027-111">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="eb027-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="eb027-112">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb027-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="eb027-113">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="eb027-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eb027-114">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eb027-115">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="eb027-116">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="eb027-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="eb027-117">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="eb027-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eb027-118">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-118">MessagePack serialization options</span></span>

<span data-ttu-id="eb027-119">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eb027-120">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-121">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eb027-122">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="eb027-122">Configure server options</span></span>

<span data-ttu-id="eb027-123">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="eb027-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eb027-124">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-124">Option</span></span> | <span data-ttu-id="eb027-125">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-125">Default Value</span></span> | <span data-ttu-id="eb027-126">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eb027-127">30 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-127">30 seconds</span></span> | <span data-ttu-id="eb027-128">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eb027-129">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="eb027-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eb027-130">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eb027-131">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-131">15 seconds</span></span> | <span data-ttu-id="eb027-132">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="eb027-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eb027-133">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-134">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-135">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-135">15 seconds</span></span> | <span data-ttu-id="eb027-136">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="eb027-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eb027-137">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eb027-138">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eb027-139">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="eb027-139">All installed protocols</span></span> | <span data-ttu-id="eb027-140">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-140">Protocols supported by this hub.</span></span> <span data-ttu-id="eb027-141">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="eb027-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eb027-142">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eb027-143">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="eb027-144">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="eb027-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="eb027-145">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="eb027-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="eb027-146">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-146">32 KB</span></span> | <span data-ttu-id="eb027-147">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="eb027-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="eb027-148">1</span><span class="sxs-lookup"><span data-stu-id="eb027-148">1</span></span> | <span data-ttu-id="eb027-149">Nombre maximal de méthodes de concentrateur que chaque client peut appeler en parallèle avant d’être mis en file d’attente.</span><span class="sxs-lookup"><span data-stu-id="eb027-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="eb027-150">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eb027-151">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="eb027-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eb027-152">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="eb027-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eb027-153">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eb027-154">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eb027-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eb027-155">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="eb027-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eb027-156">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-156">Option</span></span> | <span data-ttu-id="eb027-157">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-157">Default Value</span></span> | <span data-ttu-id="eb027-158">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eb027-159">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-159">32 KB</span></span> | <span data-ttu-id="eb027-160">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="eb027-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="eb027-161">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eb027-162">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eb027-163">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eb027-164">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-164">32 KB</span></span> | <span data-ttu-id="eb027-165">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="eb027-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="eb027-166">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eb027-167">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="eb027-167">All Transports are enabled.</span></span> | <span data-ttu-id="eb027-168">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="eb027-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eb027-169">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-169">See below.</span></span> | <span data-ttu-id="eb027-170">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="eb027-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eb027-171">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-171">See below.</span></span> | <span data-ttu-id="eb027-172">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="eb027-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="eb027-173">0</span><span class="sxs-lookup"><span data-stu-id="eb027-173">0</span></span> | <span data-ttu-id="eb027-174">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="eb027-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="eb027-175">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="eb027-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="eb027-176">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eb027-177">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-177">Option</span></span> | <span data-ttu-id="eb027-178">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-178">Default Value</span></span> | <span data-ttu-id="eb027-179">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eb027-180">90 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-180">90 seconds</span></span> | <span data-ttu-id="eb027-181">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="eb027-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eb027-182">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="eb027-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eb027-183">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eb027-184">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-184">Option</span></span> | <span data-ttu-id="eb027-185">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-185">Default Value</span></span> | <span data-ttu-id="eb027-186">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eb027-187">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-187">5 seconds</span></span> | <span data-ttu-id="eb027-188">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="eb027-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eb027-189">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="eb027-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eb027-190">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="eb027-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eb027-191">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="eb027-191">Configure client options</span></span>

<span data-ttu-id="eb027-192">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eb027-193">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="eb027-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eb027-194">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="eb027-194">Configure logging</span></span>

<span data-ttu-id="eb027-195">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eb027-196">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eb027-197">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="eb027-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-198">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eb027-199">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="eb027-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eb027-200">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb027-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eb027-201">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eb027-202">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="eb027-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eb027-203">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="eb027-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eb027-204">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="eb027-205">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="eb027-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="eb027-206">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="eb027-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="eb027-207">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-207">The following table lists the available log levels.</span></span> <span data-ttu-id="eb027-208">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="eb027-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="eb027-209">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="eb027-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="eb027-210">String</span><span class="sxs-lookup"><span data-stu-id="eb027-210">String</span></span>                      | <span data-ttu-id="eb027-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="eb027-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="eb027-212">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="eb027-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="eb027-213">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="eb027-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="eb027-214">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eb027-215">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="eb027-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eb027-216">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eb027-217">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="eb027-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eb027-218">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="eb027-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eb027-219">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="eb027-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eb027-220">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="eb027-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eb027-221">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="eb027-221">Configure allowed transports</span></span>

<span data-ttu-id="eb027-222">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eb027-223">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="eb027-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eb027-224">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-224">All transports are enabled by default.</span></span>

<span data-ttu-id="eb027-225">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="eb027-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eb027-226">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eb027-227">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="eb027-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="eb027-228">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="eb027-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="eb027-229">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb027-230">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="eb027-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eb027-231">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="eb027-231">Configure bearer authentication</span></span>

<span data-ttu-id="eb027-232">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="eb027-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eb027-233">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="eb027-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eb027-234">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="eb027-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eb027-235">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="eb027-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eb027-236">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eb027-237">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eb027-238">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="eb027-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eb027-239">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eb027-240">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="eb027-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eb027-241">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="eb027-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eb027-242">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="eb027-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-243">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-244">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-244">Option</span></span> | <span data-ttu-id="eb027-245">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-245">Default value</span></span> | <span data-ttu-id="eb027-246">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eb027-247">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-248">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-248">Timeout for server activity.</span></span> <span data-ttu-id="eb027-249">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-250">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-251">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eb027-252">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-252">15 seconds</span></span> | <span data-ttu-id="eb027-253">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-254">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-255">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-256">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-257">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-257">15 seconds</span></span> | <span data-ttu-id="eb027-258">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-259">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-260">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eb027-261">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="eb027-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eb027-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-263">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-263">Option</span></span> | <span data-ttu-id="eb027-264">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-264">Default value</span></span> | <span data-ttu-id="eb027-265">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eb027-266">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-267">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-267">Timeout for server activity.</span></span> <span data-ttu-id="eb027-268">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eb027-269">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-270">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eb027-271">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-272">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-273">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-274">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-275">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-275">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-276">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-276">Option</span></span> | <span data-ttu-id="eb027-277">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-277">Default value</span></span> | <span data-ttu-id="eb027-278">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eb027-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eb027-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eb027-280">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-281">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-281">Timeout for server activity.</span></span> <span data-ttu-id="eb027-282">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-283">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-284">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eb027-285">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-285">15 seconds</span></span> | <span data-ttu-id="eb027-286">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-287">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-288">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-289">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eb027-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eb027-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eb027-291">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-292">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-293">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-294">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eb027-295">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-295">Configure additional options</span></span>

<span data-ttu-id="eb027-296">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="eb027-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-297">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-298">Option .NET</span><span class="sxs-lookup"><span data-stu-id="eb027-298">.NET Option</span></span> |  <span data-ttu-id="eb027-299">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-299">Default value</span></span> | <span data-ttu-id="eb027-300">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eb027-301">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eb027-302">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-303">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-304">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eb027-305">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-305">Empty</span></span> | <span data-ttu-id="eb027-306">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="eb027-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eb027-307">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-307">Empty</span></span> | <span data-ttu-id="eb027-308">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="eb027-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eb027-309">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-309">Empty</span></span> | <span data-ttu-id="eb027-310">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eb027-311">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-311">5 seconds</span></span> | <span data-ttu-id="eb027-312">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="eb027-312">WebSockets only.</span></span> <span data-ttu-id="eb027-313">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="eb027-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eb027-314">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="eb027-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eb027-315">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-315">Empty</span></span> | <span data-ttu-id="eb027-316">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eb027-317">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="eb027-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eb027-318">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="eb027-319">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="eb027-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eb027-320">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="eb027-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eb027-321">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="eb027-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eb027-322">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eb027-323">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eb027-324">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="eb027-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eb027-325">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eb027-326">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="eb027-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eb027-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-328">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-328">JavaScript Option</span></span> | <span data-ttu-id="eb027-329">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-329">Default Value</span></span> | <span data-ttu-id="eb027-330">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eb027-331">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="eb027-332">Dictionnaire d’en-têtes envoyés avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="eb027-333">L’envoi d’en-têtes dans le navigateur ne fonctionne pas pour les WebSockets ou le <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flux.</span><span class="sxs-lookup"><span data-stu-id="eb027-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eb027-334">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eb027-335">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-336">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-337">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="eb027-338">Spécifie si les informations d’identification seront envoyées avec la demande CORS.</span><span class="sxs-lookup"><span data-stu-id="eb027-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="eb027-339">Azure App Service utilise cookie les s pour les sessions rémanentes et a besoin que cette option soit activée pour fonctionner correctement.</span><span class="sxs-lookup"><span data-stu-id="eb027-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="eb027-340">Pour plus d’informations sur CORS avec SignalR , consultez <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="eb027-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-341">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-341">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-342">Option Java</span><span class="sxs-lookup"><span data-stu-id="eb027-342">Java Option</span></span> | <span data-ttu-id="eb027-343">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-343">Default Value</span></span> | <span data-ttu-id="eb027-344">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eb027-345">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eb027-346">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-347">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-348">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eb027-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eb027-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eb027-350">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-350">Empty</span></span> | <span data-ttu-id="eb027-351">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eb027-352">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eb027-353">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eb027-354">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eb027-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eb027-355">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eb027-356">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eb027-357">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eb027-358">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eb027-359">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="eb027-360">`AddJsonProtocol`peut être ajouté après [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb027-361">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="eb027-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eb027-362">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="eb027-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eb027-363">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="eb027-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="eb027-364">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb027-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="eb027-365">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="eb027-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eb027-366">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eb027-367">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="eb027-368">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="eb027-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="eb027-369">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="eb027-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eb027-370">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-370">MessagePack serialization options</span></span>

<span data-ttu-id="eb027-371">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eb027-372">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-373">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eb027-374">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="eb027-374">Configure server options</span></span>

<span data-ttu-id="eb027-375">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="eb027-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eb027-376">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-376">Option</span></span> | <span data-ttu-id="eb027-377">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-377">Default Value</span></span> | <span data-ttu-id="eb027-378">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eb027-379">30 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-379">30 seconds</span></span> | <span data-ttu-id="eb027-380">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eb027-381">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="eb027-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eb027-382">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eb027-383">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-383">15 seconds</span></span> | <span data-ttu-id="eb027-384">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="eb027-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eb027-385">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-386">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-387">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-387">15 seconds</span></span> | <span data-ttu-id="eb027-388">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="eb027-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eb027-389">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eb027-390">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eb027-391">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="eb027-391">All installed protocols</span></span> | <span data-ttu-id="eb027-392">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-392">Protocols supported by this hub.</span></span> <span data-ttu-id="eb027-393">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="eb027-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eb027-394">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eb027-395">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="eb027-396">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="eb027-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="eb027-397">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="eb027-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="eb027-398">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-398">32 KB</span></span> | <span data-ttu-id="eb027-399">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="eb027-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="eb027-400">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eb027-401">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="eb027-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eb027-402">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="eb027-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eb027-403">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eb027-404">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eb027-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eb027-405">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="eb027-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eb027-406">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-406">Option</span></span> | <span data-ttu-id="eb027-407">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-407">Default Value</span></span> | <span data-ttu-id="eb027-408">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eb027-409">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-409">32 KB</span></span> | <span data-ttu-id="eb027-410">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="eb027-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="eb027-411">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eb027-412">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eb027-413">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eb027-414">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-414">32 KB</span></span> | <span data-ttu-id="eb027-415">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="eb027-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="eb027-416">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eb027-417">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="eb027-417">All Transports are enabled.</span></span> | <span data-ttu-id="eb027-418">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="eb027-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eb027-419">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-419">See below.</span></span> | <span data-ttu-id="eb027-420">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="eb027-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eb027-421">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-421">See below.</span></span> | <span data-ttu-id="eb027-422">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="eb027-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="eb027-423">0</span><span class="sxs-lookup"><span data-stu-id="eb027-423">0</span></span> | <span data-ttu-id="eb027-424">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="eb027-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="eb027-425">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="eb027-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="eb027-426">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eb027-427">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-427">Option</span></span> | <span data-ttu-id="eb027-428">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-428">Default Value</span></span> | <span data-ttu-id="eb027-429">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eb027-430">90 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-430">90 seconds</span></span> | <span data-ttu-id="eb027-431">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="eb027-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eb027-432">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="eb027-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eb027-433">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eb027-434">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-434">Option</span></span> | <span data-ttu-id="eb027-435">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-435">Default Value</span></span> | <span data-ttu-id="eb027-436">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eb027-437">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-437">5 seconds</span></span> | <span data-ttu-id="eb027-438">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="eb027-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eb027-439">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="eb027-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eb027-440">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="eb027-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eb027-441">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="eb027-441">Configure client options</span></span>

<span data-ttu-id="eb027-442">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eb027-443">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="eb027-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eb027-444">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="eb027-444">Configure logging</span></span>

<span data-ttu-id="eb027-445">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eb027-446">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eb027-447">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="eb027-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-448">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eb027-449">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="eb027-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eb027-450">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb027-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eb027-451">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eb027-452">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="eb027-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eb027-453">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="eb027-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eb027-454">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="eb027-455">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="eb027-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="eb027-456">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="eb027-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="eb027-457">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-457">The following table lists the available log levels.</span></span> <span data-ttu-id="eb027-458">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="eb027-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="eb027-459">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="eb027-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="eb027-460">String</span><span class="sxs-lookup"><span data-stu-id="eb027-460">String</span></span>                      | <span data-ttu-id="eb027-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="eb027-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="eb027-462">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="eb027-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="eb027-463">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="eb027-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="eb027-464">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eb027-465">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="eb027-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eb027-466">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eb027-467">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="eb027-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eb027-468">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="eb027-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eb027-469">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="eb027-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eb027-470">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="eb027-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eb027-471">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="eb027-471">Configure allowed transports</span></span>

<span data-ttu-id="eb027-472">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eb027-473">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="eb027-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eb027-474">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-474">All transports are enabled by default.</span></span>

<span data-ttu-id="eb027-475">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="eb027-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eb027-476">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eb027-477">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="eb027-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="eb027-478">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="eb027-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="eb027-479">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb027-480">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="eb027-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eb027-481">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="eb027-481">Configure bearer authentication</span></span>

<span data-ttu-id="eb027-482">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="eb027-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eb027-483">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="eb027-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eb027-484">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="eb027-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eb027-485">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="eb027-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eb027-486">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eb027-487">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eb027-488">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="eb027-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eb027-489">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eb027-490">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="eb027-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eb027-491">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="eb027-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eb027-492">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="eb027-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-493">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-494">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-494">Option</span></span> | <span data-ttu-id="eb027-495">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-495">Default value</span></span> | <span data-ttu-id="eb027-496">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eb027-497">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-498">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-498">Timeout for server activity.</span></span> <span data-ttu-id="eb027-499">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-500">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-501">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eb027-502">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-502">15 seconds</span></span> | <span data-ttu-id="eb027-503">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-504">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-505">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-506">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-507">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-507">15 seconds</span></span> | <span data-ttu-id="eb027-508">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-509">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-510">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eb027-511">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="eb027-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eb027-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-513">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-513">Option</span></span> | <span data-ttu-id="eb027-514">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-514">Default value</span></span> | <span data-ttu-id="eb027-515">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eb027-516">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-517">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-517">Timeout for server activity.</span></span> <span data-ttu-id="eb027-518">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eb027-519">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-520">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eb027-521">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-522">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-523">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-524">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-525">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-525">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-526">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-526">Option</span></span> | <span data-ttu-id="eb027-527">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-527">Default value</span></span> | <span data-ttu-id="eb027-528">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eb027-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eb027-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eb027-530">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-531">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-531">Timeout for server activity.</span></span> <span data-ttu-id="eb027-532">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-533">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-534">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eb027-535">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-535">15 seconds</span></span> | <span data-ttu-id="eb027-536">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-537">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-538">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-539">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eb027-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eb027-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eb027-541">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-542">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-543">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-544">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eb027-545">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-545">Configure additional options</span></span>

<span data-ttu-id="eb027-546">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="eb027-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-547">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-548">Option .NET</span><span class="sxs-lookup"><span data-stu-id="eb027-548">.NET Option</span></span> |  <span data-ttu-id="eb027-549">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-549">Default value</span></span> | <span data-ttu-id="eb027-550">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eb027-551">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eb027-552">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-553">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-554">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eb027-555">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-555">Empty</span></span> | <span data-ttu-id="eb027-556">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="eb027-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eb027-557">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-557">Empty</span></span> | <span data-ttu-id="eb027-558">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="eb027-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eb027-559">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-559">Empty</span></span> | <span data-ttu-id="eb027-560">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eb027-561">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-561">5 seconds</span></span> | <span data-ttu-id="eb027-562">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="eb027-562">WebSockets only.</span></span> <span data-ttu-id="eb027-563">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="eb027-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eb027-564">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="eb027-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eb027-565">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-565">Empty</span></span> | <span data-ttu-id="eb027-566">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eb027-567">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="eb027-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eb027-568">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="eb027-569">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="eb027-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eb027-570">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="eb027-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eb027-571">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="eb027-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eb027-572">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eb027-573">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eb027-574">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="eb027-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eb027-575">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eb027-576">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="eb027-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eb027-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-578">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-578">JavaScript Option</span></span> | <span data-ttu-id="eb027-579">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-579">Default Value</span></span> | <span data-ttu-id="eb027-580">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eb027-581">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eb027-582">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eb027-583">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-584">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-585">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-586">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-586">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-587">Option Java</span><span class="sxs-lookup"><span data-stu-id="eb027-587">Java Option</span></span> | <span data-ttu-id="eb027-588">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-588">Default Value</span></span> | <span data-ttu-id="eb027-589">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eb027-590">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eb027-591">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-592">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-593">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eb027-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eb027-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eb027-595">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-595">Empty</span></span> | <span data-ttu-id="eb027-596">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eb027-597">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eb027-598">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eb027-599">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eb027-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eb027-600">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eb027-601">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eb027-602">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eb027-603">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eb027-604">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="eb027-605">`AddJsonProtocol`peut être ajouté après [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb027-606">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="eb027-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eb027-607">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="eb027-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eb027-608">Pour plus d’informations, consultez la [System.Text.Jsdans la documentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="eb027-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="eb027-609">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb027-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="eb027-610">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="eb027-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eb027-611">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eb027-612">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="eb027-613">Basculer vers Newtonsoft.Jssur</span><span class="sxs-lookup"><span data-stu-id="eb027-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="eb027-614">Si vous avez besoin de fonctionnalités de `Newtonsoft.Json` qui ne sont pas prises en charge dans `System.Text.Json` , consultez [basculer vers Newtonsoft.Jssur](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="eb027-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eb027-615">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-615">MessagePack serialization options</span></span>

<span data-ttu-id="eb027-616">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eb027-617">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-618">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eb027-619">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="eb027-619">Configure server options</span></span>

<span data-ttu-id="eb027-620">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="eb027-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eb027-621">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-621">Option</span></span> | <span data-ttu-id="eb027-622">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-622">Default Value</span></span> | <span data-ttu-id="eb027-623">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eb027-624">30 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-624">30 seconds</span></span> | <span data-ttu-id="eb027-625">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eb027-626">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="eb027-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eb027-627">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eb027-628">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-628">15 seconds</span></span> | <span data-ttu-id="eb027-629">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="eb027-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eb027-630">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-631">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-632">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-632">15 seconds</span></span> | <span data-ttu-id="eb027-633">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="eb027-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eb027-634">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eb027-635">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eb027-636">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="eb027-636">All installed protocols</span></span> | <span data-ttu-id="eb027-637">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-637">Protocols supported by this hub.</span></span> <span data-ttu-id="eb027-638">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="eb027-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eb027-639">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eb027-640">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="eb027-641">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="eb027-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="eb027-642">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="eb027-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="eb027-643">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-643">32 KB</span></span> | <span data-ttu-id="eb027-644">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="eb027-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="eb027-645">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eb027-646">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="eb027-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eb027-647">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="eb027-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eb027-648">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eb027-649">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eb027-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eb027-650">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="eb027-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eb027-651">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-651">Option</span></span> | <span data-ttu-id="eb027-652">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-652">Default Value</span></span> | <span data-ttu-id="eb027-653">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eb027-654">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-654">32 KB</span></span> | <span data-ttu-id="eb027-655">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="eb027-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="eb027-656">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eb027-657">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eb027-658">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eb027-659">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-659">32 KB</span></span> | <span data-ttu-id="eb027-660">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="eb027-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="eb027-661">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eb027-662">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="eb027-662">All Transports are enabled.</span></span> | <span data-ttu-id="eb027-663">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="eb027-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eb027-664">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-664">See below.</span></span> | <span data-ttu-id="eb027-665">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="eb027-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eb027-666">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-666">See below.</span></span> | <span data-ttu-id="eb027-667">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="eb027-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="eb027-668">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eb027-669">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-669">Option</span></span> | <span data-ttu-id="eb027-670">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-670">Default Value</span></span> | <span data-ttu-id="eb027-671">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eb027-672">90 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-672">90 seconds</span></span> | <span data-ttu-id="eb027-673">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="eb027-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eb027-674">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="eb027-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eb027-675">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eb027-676">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-676">Option</span></span> | <span data-ttu-id="eb027-677">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-677">Default Value</span></span> | <span data-ttu-id="eb027-678">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eb027-679">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-679">5 seconds</span></span> | <span data-ttu-id="eb027-680">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="eb027-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eb027-681">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="eb027-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eb027-682">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="eb027-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eb027-683">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="eb027-683">Configure client options</span></span>

<span data-ttu-id="eb027-684">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eb027-685">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="eb027-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eb027-686">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="eb027-686">Configure logging</span></span>

<span data-ttu-id="eb027-687">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eb027-688">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eb027-689">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="eb027-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-690">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eb027-691">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="eb027-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eb027-692">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb027-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eb027-693">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eb027-694">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="eb027-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eb027-695">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="eb027-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eb027-696">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="eb027-697">Au lieu d’une `LogLevel` valeur, vous pouvez également fournir une `string` valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="eb027-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="eb027-698">Cela est utile lors SignalR de la configuration de la journalisation dans les environnements où vous n’avez pas accès aux `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="eb027-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="eb027-699">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-699">The following table lists the available log levels.</span></span> <span data-ttu-id="eb027-700">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="eb027-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="eb027-701">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="eb027-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="eb027-702">String</span><span class="sxs-lookup"><span data-stu-id="eb027-702">String</span></span>                      | <span data-ttu-id="eb027-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="eb027-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="eb027-704">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="eb027-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="eb027-705">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="eb027-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="eb027-706">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eb027-707">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="eb027-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eb027-708">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eb027-709">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="eb027-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eb027-710">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="eb027-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eb027-711">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="eb027-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eb027-712">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="eb027-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eb027-713">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="eb027-713">Configure allowed transports</span></span>

<span data-ttu-id="eb027-714">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eb027-715">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="eb027-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eb027-716">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-716">All transports are enabled by default.</span></span>

<span data-ttu-id="eb027-717">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="eb027-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eb027-718">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eb027-719">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="eb027-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="eb027-720">Dans le client Java, le transport est sélectionné à l’aide de la `withTransport` méthode sur le `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="eb027-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="eb027-721">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb027-722">Le SignalR client Java ne prend pas encore en charge le secours pour le transport.</span><span class="sxs-lookup"><span data-stu-id="eb027-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eb027-723">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="eb027-723">Configure bearer authentication</span></span>

<span data-ttu-id="eb027-724">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="eb027-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eb027-725">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="eb027-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eb027-726">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="eb027-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eb027-727">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="eb027-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eb027-728">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eb027-729">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eb027-730">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="eb027-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eb027-731">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eb027-732">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="eb027-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eb027-733">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="eb027-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eb027-734">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="eb027-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-735">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-736">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-736">Option</span></span> | <span data-ttu-id="eb027-737">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-737">Default value</span></span> | <span data-ttu-id="eb027-738">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eb027-739">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-740">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-740">Timeout for server activity.</span></span> <span data-ttu-id="eb027-741">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-742">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-743">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eb027-744">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-744">15 seconds</span></span> | <span data-ttu-id="eb027-745">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-746">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-747">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-748">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-749">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-749">15 seconds</span></span> | <span data-ttu-id="eb027-750">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-751">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-752">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eb027-753">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="eb027-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eb027-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-755">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-755">Option</span></span> | <span data-ttu-id="eb027-756">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-756">Default value</span></span> | <span data-ttu-id="eb027-757">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eb027-758">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-759">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-759">Timeout for server activity.</span></span> <span data-ttu-id="eb027-760">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eb027-761">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-762">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eb027-763">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-764">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-765">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-766">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-767">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-767">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-768">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-768">Option</span></span> | <span data-ttu-id="eb027-769">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-769">Default value</span></span> | <span data-ttu-id="eb027-770">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eb027-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eb027-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eb027-772">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-773">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-773">Timeout for server activity.</span></span> <span data-ttu-id="eb027-774">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-775">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-776">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eb027-777">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-777">15 seconds</span></span> | <span data-ttu-id="eb027-778">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-779">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-780">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-781">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eb027-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eb027-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eb027-783">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-784">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-785">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-786">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eb027-787">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-787">Configure additional options</span></span>

<span data-ttu-id="eb027-788">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="eb027-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-789">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-790">Option .NET</span><span class="sxs-lookup"><span data-stu-id="eb027-790">.NET Option</span></span> |  <span data-ttu-id="eb027-791">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-791">Default value</span></span> | <span data-ttu-id="eb027-792">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eb027-793">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eb027-794">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-795">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-796">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eb027-797">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-797">Empty</span></span> | <span data-ttu-id="eb027-798">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="eb027-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eb027-799">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-799">Empty</span></span> | <span data-ttu-id="eb027-800">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="eb027-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eb027-801">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-801">Empty</span></span> | <span data-ttu-id="eb027-802">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eb027-803">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-803">5 seconds</span></span> | <span data-ttu-id="eb027-804">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="eb027-804">WebSockets only.</span></span> <span data-ttu-id="eb027-805">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="eb027-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eb027-806">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="eb027-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eb027-807">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-807">Empty</span></span> | <span data-ttu-id="eb027-808">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eb027-809">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="eb027-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eb027-810">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="eb027-811">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="eb027-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eb027-812">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="eb027-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eb027-813">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="eb027-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eb027-814">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eb027-815">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eb027-816">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="eb027-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eb027-817">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eb027-818">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="eb027-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eb027-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-820">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-820">JavaScript Option</span></span> | <span data-ttu-id="eb027-821">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-821">Default Value</span></span> | <span data-ttu-id="eb027-822">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eb027-823">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eb027-824">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eb027-825">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-826">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-827">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-828">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-828">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-829">Option Java</span><span class="sxs-lookup"><span data-stu-id="eb027-829">Java Option</span></span> | <span data-ttu-id="eb027-830">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-830">Default Value</span></span> | <span data-ttu-id="eb027-831">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eb027-832">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eb027-833">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-834">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-835">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eb027-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eb027-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eb027-837">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-837">Empty</span></span> | <span data-ttu-id="eb027-838">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eb027-839">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eb027-840">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eb027-841">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eb027-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eb027-842">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eb027-843">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eb027-844">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eb027-845">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eb027-846">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être ajoutée après l' [ SignalR Ajout](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de votre `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eb027-847">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="eb027-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eb027-848">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un `JsonSerializerSettings` objet JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="eb027-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eb027-849">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="eb027-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="eb027-850">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb027-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="eb027-851">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="eb027-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eb027-852">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eb027-853">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eb027-854">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-854">MessagePack serialization options</span></span>

<span data-ttu-id="eb027-855">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eb027-856">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-857">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eb027-858">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="eb027-858">Configure server options</span></span>

<span data-ttu-id="eb027-859">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="eb027-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eb027-860">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-860">Option</span></span> | <span data-ttu-id="eb027-861">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-861">Default Value</span></span> | <span data-ttu-id="eb027-862">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eb027-863">30 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-863">30 seconds</span></span> | <span data-ttu-id="eb027-864">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eb027-865">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="eb027-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eb027-866">La valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eb027-867">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-867">15 seconds</span></span> | <span data-ttu-id="eb027-868">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="eb027-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eb027-869">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-870">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-871">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-871">15 seconds</span></span> | <span data-ttu-id="eb027-872">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="eb027-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eb027-873">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eb027-874">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eb027-875">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="eb027-875">All installed protocols</span></span> | <span data-ttu-id="eb027-876">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-876">Protocols supported by this hub.</span></span> <span data-ttu-id="eb027-877">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="eb027-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eb027-878">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eb027-879">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="eb027-880">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eb027-881">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="eb027-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eb027-882">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="eb027-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eb027-883">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eb027-884">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eb027-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eb027-885">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="eb027-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eb027-886">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-886">Option</span></span> | <span data-ttu-id="eb027-887">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-887">Default Value</span></span> | <span data-ttu-id="eb027-888">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eb027-889">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-889">32 KB</span></span> | <span data-ttu-id="eb027-890">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="eb027-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="eb027-891">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eb027-892">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eb027-893">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eb027-894">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-894">32 KB</span></span> | <span data-ttu-id="eb027-895">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="eb027-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="eb027-896">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eb027-897">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="eb027-897">All Transports are enabled.</span></span> | <span data-ttu-id="eb027-898">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="eb027-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eb027-899">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-899">See below.</span></span> | <span data-ttu-id="eb027-900">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="eb027-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eb027-901">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-901">See below.</span></span> | <span data-ttu-id="eb027-902">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="eb027-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="eb027-903">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eb027-904">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-904">Option</span></span> | <span data-ttu-id="eb027-905">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-905">Default Value</span></span> | <span data-ttu-id="eb027-906">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eb027-907">90 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-907">90 seconds</span></span> | <span data-ttu-id="eb027-908">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="eb027-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eb027-909">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="eb027-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eb027-910">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eb027-911">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-911">Option</span></span> | <span data-ttu-id="eb027-912">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-912">Default Value</span></span> | <span data-ttu-id="eb027-913">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eb027-914">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-914">5 seconds</span></span> | <span data-ttu-id="eb027-915">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="eb027-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eb027-916">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="eb027-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eb027-917">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="eb027-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eb027-918">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="eb027-918">Configure client options</span></span>

<span data-ttu-id="eb027-919">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eb027-920">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="eb027-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eb027-921">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="eb027-921">Configure logging</span></span>

<span data-ttu-id="eb027-922">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eb027-923">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eb027-924">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="eb027-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-925">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eb027-926">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="eb027-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eb027-927">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb027-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eb027-928">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eb027-929">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="eb027-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eb027-930">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="eb027-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eb027-931">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb027-932">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eb027-933">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="eb027-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eb027-934">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eb027-935">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="eb027-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eb027-936">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="eb027-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eb027-937">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="eb027-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eb027-938">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="eb027-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eb027-939">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="eb027-939">Configure allowed transports</span></span>

<span data-ttu-id="eb027-940">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eb027-941">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="eb027-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eb027-942">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-942">All transports are enabled by default.</span></span>

<span data-ttu-id="eb027-943">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="eb027-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eb027-944">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eb027-945">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="eb027-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eb027-946">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="eb027-946">Configure bearer authentication</span></span>

<span data-ttu-id="eb027-947">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="eb027-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eb027-948">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="eb027-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eb027-949">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="eb027-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eb027-950">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="eb027-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eb027-951">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eb027-952">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eb027-953">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="eb027-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eb027-954">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eb027-955">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="eb027-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eb027-956">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="eb027-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eb027-957">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="eb027-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-958">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-959">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-959">Option</span></span> | <span data-ttu-id="eb027-960">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-960">Default value</span></span> | <span data-ttu-id="eb027-961">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eb027-962">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-963">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-963">Timeout for server activity.</span></span> <span data-ttu-id="eb027-964">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-965">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-966">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eb027-967">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-967">15 seconds</span></span> | <span data-ttu-id="eb027-968">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-969">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-970">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-971">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-972">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-972">15 seconds</span></span> | <span data-ttu-id="eb027-973">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-974">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-975">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eb027-976">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="eb027-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eb027-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-978">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-978">Option</span></span> | <span data-ttu-id="eb027-979">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-979">Default value</span></span> | <span data-ttu-id="eb027-980">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eb027-981">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-982">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-982">Timeout for server activity.</span></span> <span data-ttu-id="eb027-983">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eb027-984">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-985">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eb027-986">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-987">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-988">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-989">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-990">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-990">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-991">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-991">Option</span></span> | <span data-ttu-id="eb027-992">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-992">Default value</span></span> | <span data-ttu-id="eb027-993">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eb027-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eb027-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eb027-995">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-996">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-996">Timeout for server activity.</span></span> <span data-ttu-id="eb027-997">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-998">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-999">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eb027-1000">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1000">15 seconds</span></span> | <span data-ttu-id="eb027-1001">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-1002">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-1003">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-1004">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eb027-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eb027-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eb027-1006">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eb027-1007">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eb027-1008">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="eb027-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eb027-1009">Si le client n’a pas envoyé de message dans le `ClientTimeoutInterval` jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="eb027-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eb027-1010">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-1010">Configure additional options</span></span>

<span data-ttu-id="eb027-1011">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="eb027-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-1013">Option .NET</span><span class="sxs-lookup"><span data-stu-id="eb027-1013">.NET Option</span></span> |  <span data-ttu-id="eb027-1014">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1014">Default value</span></span> | <span data-ttu-id="eb027-1015">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eb027-1016">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eb027-1017">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-1018">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-1019">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eb027-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1020">Empty</span></span> | <span data-ttu-id="eb027-1021">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="eb027-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eb027-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1022">Empty</span></span> | <span data-ttu-id="eb027-1023">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="eb027-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eb027-1024">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1024">Empty</span></span> | <span data-ttu-id="eb027-1025">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eb027-1026">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1026">5 seconds</span></span> | <span data-ttu-id="eb027-1027">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="eb027-1027">WebSockets only.</span></span> <span data-ttu-id="eb027-1028">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="eb027-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eb027-1029">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="eb027-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eb027-1030">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1030">Empty</span></span> | <span data-ttu-id="eb027-1031">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eb027-1032">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="eb027-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eb027-1033">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="eb027-1034">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="eb027-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eb027-1035">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="eb027-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eb027-1036">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="eb027-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eb027-1037">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eb027-1038">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eb027-1039">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="eb027-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eb027-1040">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eb027-1041">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="eb027-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eb027-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-1043">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-1043">JavaScript Option</span></span> | <span data-ttu-id="eb027-1044">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1044">Default Value</span></span> | <span data-ttu-id="eb027-1045">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eb027-1046">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eb027-1047">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eb027-1048">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-1049">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-1050">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-1051">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-1052">Option Java</span><span class="sxs-lookup"><span data-stu-id="eb027-1052">Java Option</span></span> | <span data-ttu-id="eb027-1053">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1053">Default Value</span></span> | <span data-ttu-id="eb027-1054">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eb027-1055">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eb027-1056">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-1057">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-1058">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eb027-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eb027-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eb027-1060">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1060">Empty</span></span> | <span data-ttu-id="eb027-1061">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eb027-1062">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eb027-1063">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eb027-1064">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eb027-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eb027-1065">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eb027-1066">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eb027-1067">ASP.NET Core SignalR prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eb027-1068">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eb027-1069">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être ajoutée après l' [ SignalR Ajout](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de votre `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eb027-1070">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="eb027-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eb027-1071">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un `JsonSerializerSettings` objet JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="eb027-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eb027-1072">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="eb027-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="eb027-1073">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="eb027-1074">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="eb027-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eb027-1075">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eb027-1076">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eb027-1077">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="eb027-1077">MessagePack serialization options</span></span>

<span data-ttu-id="eb027-1078">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eb027-1079">Pour plus d’informations, consultez [MessagePack dans SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="eb027-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-1080">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="eb027-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eb027-1081">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="eb027-1081">Configure server options</span></span>

<span data-ttu-id="eb027-1082">Le tableau suivant décrit les options de configuration des SignalR hubs :</span><span class="sxs-lookup"><span data-stu-id="eb027-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eb027-1083">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-1083">Option</span></span> | <span data-ttu-id="eb027-1084">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1084">Default Value</span></span> | <span data-ttu-id="eb027-1085">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="eb027-1086">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1086">15 seconds</span></span> | <span data-ttu-id="eb027-1087">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="eb027-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eb027-1088">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-1089">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eb027-1090">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1090">15 seconds</span></span> | <span data-ttu-id="eb027-1091">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="eb027-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eb027-1092">Lors de la modification `KeepAliveInterval` , modifiez le `ServerTimeout` / `serverTimeoutInMilliseconds` paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eb027-1093">La `ServerTimeout` / `serverTimeoutInMilliseconds` valeur recommandée est le double de la `KeepAliveInterval` valeur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eb027-1094">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="eb027-1094">All installed protocols</span></span> | <span data-ttu-id="eb027-1095">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="eb027-1096">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="eb027-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eb027-1097">Si `true` la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eb027-1098">La valeur par défaut est `false` , car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="eb027-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="eb027-1099">Les options peuvent être configurées pour tous les hubs en fournissant un délégué d’options à l' `AddSignalR` appel dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb027-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eb027-1100">Les options d’un seul Hub remplacent les options globales fournies dans `AddSignalR` et peuvent être configurées à l’aide de <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="eb027-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eb027-1101">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="eb027-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eb027-1102">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eb027-1103">Ces options sont configurées en passant un délégué [à \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="eb027-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eb027-1104">Le tableau suivant décrit les options de configuration des SignalR options http avancées de ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="eb027-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eb027-1105">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-1105">Option</span></span> | <span data-ttu-id="eb027-1106">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1106">Default Value</span></span> | <span data-ttu-id="eb027-1107">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eb027-1108">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-1108">32 KB</span></span> | <span data-ttu-id="eb027-1109">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="eb027-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="eb027-1110">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eb027-1111">Données collectées automatiquement à partir des `Authorize` attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eb027-1112">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eb027-1113">32 Ko</span><span class="sxs-lookup"><span data-stu-id="eb027-1113">32 KB</span></span> | <span data-ttu-id="eb027-1114">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="eb027-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="eb027-1115">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="eb027-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eb027-1116">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="eb027-1116">All Transports are enabled.</span></span> | <span data-ttu-id="eb027-1117">Énumération de bits indicateurs des `HttpTransportType` valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="eb027-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eb027-1118">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-1118">See below.</span></span> | <span data-ttu-id="eb027-1119">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="eb027-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eb027-1120">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="eb027-1120">See below.</span></span> | <span data-ttu-id="eb027-1121">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="eb027-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="eb027-1122">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eb027-1123">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-1123">Option</span></span> | <span data-ttu-id="eb027-1124">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1124">Default Value</span></span> | <span data-ttu-id="eb027-1125">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eb027-1126">90 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1126">90 seconds</span></span> | <span data-ttu-id="eb027-1127">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="eb027-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eb027-1128">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="eb027-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eb027-1129">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `WebSockets` propriété :</span><span class="sxs-lookup"><span data-stu-id="eb027-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eb027-1130">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-1130">Option</span></span> | <span data-ttu-id="eb027-1131">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1131">Default Value</span></span> | <span data-ttu-id="eb027-1132">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eb027-1133">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1133">5 seconds</span></span> | <span data-ttu-id="eb027-1134">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="eb027-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eb027-1135">Délégué qui peut être utilisé pour affecter `Sec-WebSocket-Protocol` une valeur personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="eb027-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eb027-1136">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="eb027-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eb027-1137">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="eb027-1137">Configure client options</span></span>

<span data-ttu-id="eb027-1138">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eb027-1139">Il est également disponible dans le client Java, mais la sous `HttpHubConnectionBuilder` -classe est ce qui contient les options de configuration du générateur, ainsi que sur le `HubConnection` lui-même.</span><span class="sxs-lookup"><span data-stu-id="eb027-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eb027-1140">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="eb027-1140">Configure logging</span></span>

<span data-ttu-id="eb027-1141">La journalisation est configurée dans le client .NET à l’aide de la `ConfigureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eb027-1142">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eb027-1143">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="eb027-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eb027-1144">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eb027-1145">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="eb027-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eb027-1146">Par exemple, pour activer la journalisation de la console, installez le `Microsoft.Extensions.Logging.Console` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb027-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eb027-1147">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="eb027-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eb027-1148">Dans le client JavaScript, `configureLogging` il existe une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="eb027-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eb027-1149">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="eb027-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eb027-1150">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb027-1151">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="eb027-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eb027-1152">Pour plus d’informations sur la journalisation, consultez la [ SignalR documentation relative aux diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="eb027-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eb027-1153">Le SignalR client Java utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eb027-1154">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="eb027-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eb027-1155">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="eb027-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eb027-1156">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="eb027-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eb027-1157">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="eb027-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eb027-1158">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="eb027-1158">Configure allowed transports</span></span>

<span data-ttu-id="eb027-1159">Les transports utilisés par SignalR peuvent être configurés dans l' `WithUrl` appel ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eb027-1160">Une opération or au niveau du bit des valeurs de `HttpTransportType` peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="eb027-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eb027-1161">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="eb027-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="eb027-1162">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="eb027-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eb027-1163">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d’options fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eb027-1164">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="eb027-1164">Configure bearer authentication</span></span>

<span data-ttu-id="eb027-1165">Pour fournir des données d’authentification avec SignalR les demandes, utilisez l' `AccessTokenProvider` option ( `accessTokenFactory` en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="eb027-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eb027-1166">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP (à l’aide de l' `Authorization` en-tête de type `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="eb027-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eb027-1167">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="eb027-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eb027-1168">Dans ce cas, le jeton d’accès est fourni sous la forme d’une valeur de chaîne de requête `access_token` .</span><span class="sxs-lookup"><span data-stu-id="eb027-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eb027-1169">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué options dans `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eb027-1170">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet d’options dans `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eb027-1171">Dans le SignalR client Java, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="eb027-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eb027-1172">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir un [RxJava](https://github.com/ReactiveX/RxJava) [unique \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="eb027-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eb027-1173">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="eb027-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eb027-1174">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="eb027-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eb027-1175">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive sont disponibles sur l' `HubConnection` objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="eb027-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-1177">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-1177">Option</span></span> | <span data-ttu-id="eb027-1178">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1178">Default value</span></span> | <span data-ttu-id="eb027-1179">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eb027-1180">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-1181">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1181">Timeout for server activity.</span></span> <span data-ttu-id="eb027-1182">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-1183">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-1184">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eb027-1185">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1185">15 seconds</span></span> | <span data-ttu-id="eb027-1186">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-1187">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `Closed` événement ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="eb027-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eb027-1188">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-1189">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="eb027-1190">Dans le client .NET, les valeurs de délai d’attente sont spécifiées en tant que `TimeSpan` valeurs.</span><span class="sxs-lookup"><span data-stu-id="eb027-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eb027-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-1192">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-1192">Option</span></span> | <span data-ttu-id="eb027-1193">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1193">Default value</span></span> | <span data-ttu-id="eb027-1194">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eb027-1195">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-1196">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1196">Timeout for server activity.</span></span> <span data-ttu-id="eb027-1197">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onclose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eb027-1198">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-1199">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-1200">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-1201">Option</span><span class="sxs-lookup"><span data-stu-id="eb027-1201">Option</span></span> | <span data-ttu-id="eb027-1202">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1202">Default value</span></span> | <span data-ttu-id="eb027-1203">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eb027-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eb027-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eb027-1205">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="eb027-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eb027-1206">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1206">Timeout for server activity.</span></span> <span data-ttu-id="eb027-1207">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-1208">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="eb027-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eb027-1209">La valeur recommandée est un nombre au moins double de la valeur du serveur `KeepAliveInterval` , pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="eb027-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eb027-1210">15 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1210">15 seconds</span></span> | <span data-ttu-id="eb027-1211">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="eb027-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="eb027-1212">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche l' `onClose` événement.</span><span class="sxs-lookup"><span data-stu-id="eb027-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eb027-1213">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="eb027-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eb027-1214">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [ SignalR spécification du protocole Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="eb027-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eb027-1215">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-1215">Configure additional options</span></span>

<span data-ttu-id="eb027-1216">Des options supplémentaires peuvent être configurées dans la `WithUrl` `withUrl` méthode (en JavaScript) sur `HubConnectionBuilder` ou sur les différentes API de configuration sur le `HttpHubConnectionBuilder` dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="eb027-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eb027-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="eb027-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eb027-1218">Option .NET</span><span class="sxs-lookup"><span data-stu-id="eb027-1218">.NET Option</span></span> |  <span data-ttu-id="eb027-1219">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1219">Default value</span></span> | <span data-ttu-id="eb027-1220">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eb027-1221">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eb027-1222">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-1223">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-1224">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eb027-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1225">Empty</span></span> | <span data-ttu-id="eb027-1226">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="eb027-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eb027-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1227">Empty</span></span> | <span data-ttu-id="eb027-1228">Collection de HTTP cookie s à envoyer avec chaque requête http.</span><span class="sxs-lookup"><span data-stu-id="eb027-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eb027-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1229">Empty</span></span> | <span data-ttu-id="eb027-1230">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eb027-1231">5 secondes</span><span class="sxs-lookup"><span data-stu-id="eb027-1231">5 seconds</span></span> | <span data-ttu-id="eb027-1232">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="eb027-1232">WebSockets only.</span></span> <span data-ttu-id="eb027-1233">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="eb027-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eb027-1234">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="eb027-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eb027-1235">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1235">Empty</span></span> | <span data-ttu-id="eb027-1236">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eb027-1237">Délégué qui peut être utilisé pour configurer ou remplacer le `HttpMessageHandler` utilisé pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="eb027-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eb027-1238">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="eb027-1239">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="eb027-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eb027-1240">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou retournez une nouvelle `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="eb027-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eb027-1241">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que Cookie s et en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="eb027-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eb027-1242">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eb027-1243">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="eb027-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eb027-1244">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="eb027-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eb027-1245">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="eb027-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eb027-1246">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="eb027-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eb027-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eb027-1248">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="eb027-1248">JavaScript Option</span></span> | <span data-ttu-id="eb027-1249">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1249">Default Value</span></span> | <span data-ttu-id="eb027-1250">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eb027-1251">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eb027-1252">Affectez `true` la valeur à pour consigner les octets/caractères des messages envoyés et reçus par le client.</span><span class="sxs-lookup"><span data-stu-id="eb027-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eb027-1253">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-1254">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-1255">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eb027-1256">Java</span><span class="sxs-lookup"><span data-stu-id="eb027-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="eb027-1257">Option Java</span><span class="sxs-lookup"><span data-stu-id="eb027-1257">Java Option</span></span> | <span data-ttu-id="eb027-1258">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="eb027-1258">Default Value</span></span> | <span data-ttu-id="eb027-1259">Description</span><span class="sxs-lookup"><span data-stu-id="eb027-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eb027-1260">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eb027-1261">Affectez `true` la valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="eb027-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eb027-1262">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="eb027-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eb027-1263">Ce paramètre ne peut pas être activé lors de l’utilisation du SignalR service Azure.</span><span class="sxs-lookup"><span data-stu-id="eb027-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eb027-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eb027-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eb027-1265">Empty</span><span class="sxs-lookup"><span data-stu-id="eb027-1265">Empty</span></span> | <span data-ttu-id="eb027-1266">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb027-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eb027-1267">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni à `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eb027-1268">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="eb027-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eb027-1269">Dans le client Java, ces options peuvent être configurées avec les méthodes sur le `HttpHubConnectionBuilder` retourné à partir de l’option `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eb027-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eb027-1270">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eb027-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
