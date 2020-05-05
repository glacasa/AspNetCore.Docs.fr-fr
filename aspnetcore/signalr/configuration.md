---
title: Configuration SignalR de ASP.net Core
author: bradygaster
description: Découvrez comment configurer des applications SignalR ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 054462c37fffd1973cbbe4f76ae4a3be5a6c1778
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767302"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="a4e1f-103">Configuration d’ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="a4e1f-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4e1f-104">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-105">ASP.NET Core Signalr prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4e1f-106">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4e1f-107">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a4e1f-108">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4e1f-109">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4e1f-110">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4e1f-111">Pour plus d’informations, consultez la [Documentation System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a4e1f-112">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par `Startup.ConfigureServices`défaut, utilisez le code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a4e1f-113">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4e1f-114">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4e1f-115">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a4e1f-116">Basculer vers Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="a4e1f-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a4e1f-117">Si vous avez besoin de `Newtonsoft.Json` fonctionnalités de qui ne `System.Text.Json`sont pas prises en charge dans, consultez [basculer vers Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4e1f-118">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-118">MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-119">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4e1f-120">Pour plus d’informations, consultez [MessagePack dans signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-121">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4e1f-122">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-122">Configure server options</span></span>

<span data-ttu-id="a4e1f-123">Le tableau suivant décrit les options de configuration des hubs Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4e1f-124">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-124">Option</span></span> | <span data-ttu-id="a4e1f-125">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-125">Default Value</span></span> | <span data-ttu-id="a4e1f-126">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4e1f-127">30 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-127">30 seconds</span></span> | <span data-ttu-id="a4e1f-128">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4e1f-129">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4e1f-130">La valeur recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-131">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-131">15 seconds</span></span> | <span data-ttu-id="a4e1f-132">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4e1f-133">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-134">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-135">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-135">15 seconds</span></span> | <span data-ttu-id="a4e1f-136">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4e1f-137">Lors de `KeepAliveInterval`la modification, `ServerTimeout` / `serverTimeoutInMilliseconds` modifiez le paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4e1f-138">La valeur `ServerTimeout` / `serverTimeoutInMilliseconds` recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4e1f-139">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-139">All installed protocols</span></span> | <span data-ttu-id="a4e1f-140">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-140">Protocols supported by this hub.</span></span> <span data-ttu-id="a4e1f-141">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4e1f-142">Si `true`la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4e1f-143">La valeur par `false`défaut est, car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a4e1f-144">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a4e1f-145">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a4e1f-146">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-146">32 KB</span></span> | <span data-ttu-id="a4e1f-147">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a4e1f-148">Les options peuvent être configurées pour tous les hubs en fournissant un délégué `AddSignalR` d’options `Startup.ConfigureServices`à l’appel dans.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4e1f-149">Les options d’un seul Hub remplacent les options globales `AddSignalR` fournies dans et peuvent être <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4e1f-150">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="a4e1f-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4e1f-151">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4e1f-152">Ces options sont configurées en passant un délégué [à\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4e1f-153">Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4e1f-154">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-154">Option</span></span> | <span data-ttu-id="a4e1f-155">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-155">Default Value</span></span> | <span data-ttu-id="a4e1f-156">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4e1f-157">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-157">32 KB</span></span> | <span data-ttu-id="a4e1f-158">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a4e1f-159">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4e1f-160">Données collectées automatiquement à `Authorize` partir des attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4e1f-161">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4e1f-162">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-162">32 KB</span></span> | <span data-ttu-id="a4e1f-163">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a4e1f-164">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4e1f-165">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-165">All Transports are enabled.</span></span> | <span data-ttu-id="a4e1f-166">Énumération de bits indicateurs `HttpTransportType` des valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4e1f-167">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-167">See below.</span></span> | <span data-ttu-id="a4e1f-168">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4e1f-169">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-169">See below.</span></span> | <span data-ttu-id="a4e1f-170">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a4e1f-171">0</span><span class="sxs-lookup"><span data-stu-id="a4e1f-171">0</span></span> | <span data-ttu-id="a4e1f-172">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a4e1f-173">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a4e1f-174">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4e1f-175">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-175">Option</span></span> | <span data-ttu-id="a4e1f-176">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-176">Default Value</span></span> | <span data-ttu-id="a4e1f-177">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4e1f-178">90 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-178">90 seconds</span></span> | <span data-ttu-id="a4e1f-179">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4e1f-180">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4e1f-181">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées `WebSockets` à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4e1f-182">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-182">Option</span></span> | <span data-ttu-id="a4e1f-183">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-183">Default Value</span></span> | <span data-ttu-id="a4e1f-184">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4e1f-185">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-185">5 seconds</span></span> | <span data-ttu-id="a4e1f-186">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4e1f-187">Délégué qui peut être utilisé pour affecter une valeur `Sec-WebSocket-Protocol` personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4e1f-188">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4e1f-189">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="a4e1f-189">Configure client options</span></span>

<span data-ttu-id="a4e1f-190">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4e1f-191">Il est également disponible dans le client Java, mais la `HttpHubConnectionBuilder` sous-classe est ce qui contient les options de configuration du générateur, ainsi `HubConnection` que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4e1f-192">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="a4e1f-192">Configure logging</span></span>

<span data-ttu-id="a4e1f-193">La journalisation est configurée dans le client `ConfigureLogging` .net à l’aide de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4e1f-194">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4e1f-195">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-196">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4e1f-197">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4e1f-198">Par exemple, pour activer la journalisation de la `Microsoft.Extensions.Logging.Console` console, installez le package NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4e1f-199">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4e1f-200">Dans le client JavaScript, il existe `configureLogging` une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4e1f-201">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4e1f-202">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a4e1f-203">Au lieu d' `LogLevel` une valeur, vous pouvez également fournir `string` une valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a4e1f-204">Cela est utile lors de la configuration de la journalisation Signalr dans les `LogLevel` environnements où vous n’avez pas accès aux constantes.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a4e1f-205">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-205">The following table lists the available log levels.</span></span> <span data-ttu-id="a4e1f-206">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a4e1f-207">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a4e1f-208">String</span><span class="sxs-lookup"><span data-stu-id="a4e1f-208">String</span></span>                      | <span data-ttu-id="a4e1f-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a4e1f-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a4e1f-210">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a4e1f-211">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a4e1f-212">Pour désactiver entièrement la journalisation `signalR.LogLevel.None` , spécifiez dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4e1f-213">Pour plus d’informations sur la journalisation, consultez la [documentation relative aux diagnostics de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4e1f-214">Le client Java Signalr utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4e1f-215">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4e1f-216">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4e1f-217">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4e1f-218">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4e1f-219">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-219">Configure allowed transports</span></span>

<span data-ttu-id="a4e1f-220">Les transports utilisés par Signalr peuvent être configurés dans l' `WithUrl` appel (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4e1f-221">Une opération or au niveau du bit des `HttpTransportType` valeurs de peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4e1f-222">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-222">All transports are enabled by default.</span></span>

<span data-ttu-id="a4e1f-223">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4e1f-224">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d' `withUrl`options fourni à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4e1f-225">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a4e1f-226">Dans le client Java, le transport est sélectionné à l' `withTransport` aide de la `HttpHubConnectionBuilder`méthode sur le.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a4e1f-227">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4e1f-228">Le client Java Signalr ne prend pas encore en charge le transport de secours.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4e1f-229">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-229">Configure bearer authentication</span></span>

<span data-ttu-id="a4e1f-230">Pour fournir des données d’authentification avec les demandes Signalr, `AccessTokenProvider` utilisez l'`accessTokenFactory` option (en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4e1f-231">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP ( `Authorization` à l’aide de l' `Bearer`en-tête de type).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4e1f-232">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4e1f-233">Dans ce cas, le jeton d’accès est fourni sous la forme d' `access_token`une valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4e1f-234">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué `WithUrl`options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4e1f-235">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet `withUrl`d’options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4e1f-236">Dans le client Java Signalr, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4e1f-237">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [RxJava](https://github.com/ReactiveX/RxJava) [>de\<chaîne unique ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4e1f-238">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4e1f-239">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="a4e1f-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4e1f-240">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive `HubConnection` sont disponibles sur l’objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-241">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-242">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-242">Option</span></span> | <span data-ttu-id="a4e1f-243">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-243">Default value</span></span> | <span data-ttu-id="a4e1f-244">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4e1f-245">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-246">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-246">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-247">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `Closed` et déclenche`onclose` l’événement (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-248">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-249">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-250">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-250">15 seconds</span></span> | <span data-ttu-id="a4e1f-251">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-252">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `Closed` l’événement`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-253">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-254">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-255">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-255">15 seconds</span></span> | <span data-ttu-id="a4e1f-256">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-257">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-258">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4e1f-259">Dans le client .NET, les valeurs de délai d' `TimeSpan` attente sont spécifiées en tant que valeurs.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-261">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-261">Option</span></span> | <span data-ttu-id="a4e1f-262">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-262">Default value</span></span> | <span data-ttu-id="a4e1f-263">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4e1f-264">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-265">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-265">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-266">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onclose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4e1f-267">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-268">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4e1f-269">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-270">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-271">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-272">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-273">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-273">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-274">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-274">Option</span></span> | <span data-ttu-id="a4e1f-275">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-275">Default value</span></span> | <span data-ttu-id="a4e1f-276">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4e1f-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4e1f-278">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-279">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-279">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-280">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onClose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-281">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-282">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4e1f-283">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-283">15 seconds</span></span> | <span data-ttu-id="a4e1f-284">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-285">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `onClose` l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-286">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-287">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4e1f-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4e1f-289">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-290">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-291">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-292">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4e1f-293">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-293">Configure additional options</span></span>

<span data-ttu-id="a4e1f-294">Des options supplémentaires peuvent être configurées `WithUrl` dans`withUrl` la méthode (en JavaScript `HubConnectionBuilder` ) sur ou sur les différentes API de `HttpHubConnectionBuilder` configuration sur le dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-295">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-296">Option .NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-296">.NET Option</span></span> |  <span data-ttu-id="a4e1f-297">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-297">Default value</span></span> | <span data-ttu-id="a4e1f-298">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-299">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4e1f-300">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-301">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-302">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4e1f-303">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-303">Empty</span></span> | <span data-ttu-id="a4e1f-304">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4e1f-305">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-305">Empty</span></span> | <span data-ttu-id="a4e1f-306">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4e1f-307">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-307">Empty</span></span> | <span data-ttu-id="a4e1f-308">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4e1f-309">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-309">5 seconds</span></span> | <span data-ttu-id="a4e1f-310">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-310">WebSockets only.</span></span> <span data-ttu-id="a4e1f-311">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4e1f-312">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4e1f-313">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-313">Empty</span></span> | <span data-ttu-id="a4e1f-314">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4e1f-315">Délégué qui peut être utilisé pour configurer ou remplacer le utilisé `HttpMessageHandler` pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4e1f-316">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4e1f-317">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4e1f-318">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou `HttpMessageHandler` retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4e1f-319">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="a4e1f-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4e1f-320">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4e1f-321">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4e1f-322">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4e1f-323">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4e1f-324">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-326">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-326">JavaScript Option</span></span> | <span data-ttu-id="a4e1f-327">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-327">Default Value</span></span> | <span data-ttu-id="a4e1f-328">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4e1f-329">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4e1f-330">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-331">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-332">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="a4e1f-333">Spécifie si les informations d’identification seront envoyées avec la demande CORS.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="a4e1f-334">Azure App Service utilise des cookies pour les sessions rémanentes et a besoin que cette option soit activée pour fonctionner correctement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="a4e1f-335">Pour plus d’informations sur CORS avec Signalr, <xref:signalr/security#cross-origin-resource-sharing>consultez.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-336">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-336">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-337">Option Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-337">Java Option</span></span> | <span data-ttu-id="a4e1f-338">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-338">Default Value</span></span> | <span data-ttu-id="a4e1f-339">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-340">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4e1f-341">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-342">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-343">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4e1f-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4e1f-345">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-345">Empty</span></span> | <span data-ttu-id="a4e1f-346">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4e1f-347">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni `WithUrl`à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4e1f-348">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4e1f-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4e1f-349">Dans le client Java, ces options peuvent être configurées avec les méthodes sur `HttpHubConnectionBuilder` le retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4e1f-350">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4e1f-351">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-352">ASP.NET Core Signalr prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4e1f-353">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4e1f-354">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a4e1f-355">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4e1f-356">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4e1f-357">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4e1f-358">Pour plus d’informations, consultez la [Documentation System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a4e1f-359">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par `Startup.ConfigureServices`défaut, utilisez le code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a4e1f-360">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4e1f-361">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4e1f-362">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a4e1f-363">Basculer vers Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="a4e1f-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a4e1f-364">Si vous avez besoin de `Newtonsoft.Json` fonctionnalités de qui ne `System.Text.Json`sont pas prises en charge dans, consultez [basculer vers Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4e1f-365">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-365">MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-366">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4e1f-367">Pour plus d’informations, consultez [MessagePack dans signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-368">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4e1f-369">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-369">Configure server options</span></span>

<span data-ttu-id="a4e1f-370">Le tableau suivant décrit les options de configuration des hubs Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4e1f-371">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-371">Option</span></span> | <span data-ttu-id="a4e1f-372">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-372">Default Value</span></span> | <span data-ttu-id="a4e1f-373">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4e1f-374">30 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-374">30 seconds</span></span> | <span data-ttu-id="a4e1f-375">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4e1f-376">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4e1f-377">La valeur recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-378">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-378">15 seconds</span></span> | <span data-ttu-id="a4e1f-379">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4e1f-380">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-381">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-382">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-382">15 seconds</span></span> | <span data-ttu-id="a4e1f-383">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4e1f-384">Lors de `KeepAliveInterval`la modification, `ServerTimeout` / `serverTimeoutInMilliseconds` modifiez le paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4e1f-385">La valeur `ServerTimeout` / `serverTimeoutInMilliseconds` recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4e1f-386">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-386">All installed protocols</span></span> | <span data-ttu-id="a4e1f-387">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-387">Protocols supported by this hub.</span></span> <span data-ttu-id="a4e1f-388">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4e1f-389">Si `true`la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4e1f-390">La valeur par `false`défaut est, car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a4e1f-391">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a4e1f-392">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a4e1f-393">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-393">32 KB</span></span> | <span data-ttu-id="a4e1f-394">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a4e1f-395">Les options peuvent être configurées pour tous les hubs en fournissant un délégué `AddSignalR` d’options `Startup.ConfigureServices`à l’appel dans.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4e1f-396">Les options d’un seul Hub remplacent les options globales `AddSignalR` fournies dans et peuvent être <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4e1f-397">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="a4e1f-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4e1f-398">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4e1f-399">Ces options sont configurées en passant un délégué [à\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4e1f-400">Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4e1f-401">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-401">Option</span></span> | <span data-ttu-id="a4e1f-402">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-402">Default Value</span></span> | <span data-ttu-id="a4e1f-403">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4e1f-404">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-404">32 KB</span></span> | <span data-ttu-id="a4e1f-405">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a4e1f-406">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4e1f-407">Données collectées automatiquement à `Authorize` partir des attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4e1f-408">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4e1f-409">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-409">32 KB</span></span> | <span data-ttu-id="a4e1f-410">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a4e1f-411">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4e1f-412">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-412">All Transports are enabled.</span></span> | <span data-ttu-id="a4e1f-413">Énumération de bits indicateurs `HttpTransportType` des valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4e1f-414">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-414">See below.</span></span> | <span data-ttu-id="a4e1f-415">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4e1f-416">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-416">See below.</span></span> | <span data-ttu-id="a4e1f-417">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a4e1f-418">0</span><span class="sxs-lookup"><span data-stu-id="a4e1f-418">0</span></span> | <span data-ttu-id="a4e1f-419">Spécifiez la version minimale du protocole Negotiate.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a4e1f-420">Cela permet de limiter les clients aux versions plus récentes.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a4e1f-421">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4e1f-422">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-422">Option</span></span> | <span data-ttu-id="a4e1f-423">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-423">Default Value</span></span> | <span data-ttu-id="a4e1f-424">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4e1f-425">90 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-425">90 seconds</span></span> | <span data-ttu-id="a4e1f-426">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4e1f-427">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4e1f-428">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées `WebSockets` à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4e1f-429">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-429">Option</span></span> | <span data-ttu-id="a4e1f-430">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-430">Default Value</span></span> | <span data-ttu-id="a4e1f-431">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4e1f-432">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-432">5 seconds</span></span> | <span data-ttu-id="a4e1f-433">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4e1f-434">Délégué qui peut être utilisé pour affecter une valeur `Sec-WebSocket-Protocol` personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4e1f-435">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4e1f-436">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="a4e1f-436">Configure client options</span></span>

<span data-ttu-id="a4e1f-437">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4e1f-438">Il est également disponible dans le client Java, mais la `HttpHubConnectionBuilder` sous-classe est ce qui contient les options de configuration du générateur, ainsi `HubConnection` que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4e1f-439">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="a4e1f-439">Configure logging</span></span>

<span data-ttu-id="a4e1f-440">La journalisation est configurée dans le client `ConfigureLogging` .net à l’aide de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4e1f-441">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4e1f-442">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-443">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4e1f-444">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4e1f-445">Par exemple, pour activer la journalisation de la `Microsoft.Extensions.Logging.Console` console, installez le package NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4e1f-446">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4e1f-447">Dans le client JavaScript, il existe `configureLogging` une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4e1f-448">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4e1f-449">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a4e1f-450">Au lieu d' `LogLevel` une valeur, vous pouvez également fournir `string` une valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a4e1f-451">Cela est utile lors de la configuration de la journalisation Signalr dans les `LogLevel` environnements où vous n’avez pas accès aux constantes.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a4e1f-452">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-452">The following table lists the available log levels.</span></span> <span data-ttu-id="a4e1f-453">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a4e1f-454">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a4e1f-455">String</span><span class="sxs-lookup"><span data-stu-id="a4e1f-455">String</span></span>                      | <span data-ttu-id="a4e1f-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a4e1f-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a4e1f-457">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a4e1f-458">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a4e1f-459">Pour désactiver entièrement la journalisation `signalR.LogLevel.None` , spécifiez dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4e1f-460">Pour plus d’informations sur la journalisation, consultez la [documentation relative aux diagnostics de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4e1f-461">Le client Java Signalr utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4e1f-462">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4e1f-463">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4e1f-464">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4e1f-465">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4e1f-466">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-466">Configure allowed transports</span></span>

<span data-ttu-id="a4e1f-467">Les transports utilisés par Signalr peuvent être configurés dans l' `WithUrl` appel (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4e1f-468">Une opération or au niveau du bit des `HttpTransportType` valeurs de peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4e1f-469">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-469">All transports are enabled by default.</span></span>

<span data-ttu-id="a4e1f-470">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4e1f-471">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d' `withUrl`options fourni à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4e1f-472">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a4e1f-473">Dans le client Java, le transport est sélectionné à l' `withTransport` aide de la `HttpHubConnectionBuilder`méthode sur le.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a4e1f-474">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4e1f-475">Le client Java Signalr ne prend pas encore en charge le transport de secours.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4e1f-476">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-476">Configure bearer authentication</span></span>

<span data-ttu-id="a4e1f-477">Pour fournir des données d’authentification avec les demandes Signalr, `AccessTokenProvider` utilisez l'`accessTokenFactory` option (en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4e1f-478">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP ( `Authorization` à l’aide de l' `Bearer`en-tête de type).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4e1f-479">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4e1f-480">Dans ce cas, le jeton d’accès est fourni sous la forme d' `access_token`une valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4e1f-481">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué `WithUrl`options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4e1f-482">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet `withUrl`d’options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4e1f-483">Dans le client Java Signalr, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4e1f-484">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [RxJava](https://github.com/ReactiveX/RxJava) [>de\<chaîne unique ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4e1f-485">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4e1f-486">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="a4e1f-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4e1f-487">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive `HubConnection` sont disponibles sur l’objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-488">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-489">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-489">Option</span></span> | <span data-ttu-id="a4e1f-490">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-490">Default value</span></span> | <span data-ttu-id="a4e1f-491">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4e1f-492">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-493">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-493">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-494">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `Closed` et déclenche`onclose` l’événement (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-495">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-496">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-497">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-497">15 seconds</span></span> | <span data-ttu-id="a4e1f-498">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-499">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `Closed` l’événement`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-500">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-501">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-502">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-502">15 seconds</span></span> | <span data-ttu-id="a4e1f-503">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-504">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-505">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4e1f-506">Dans le client .NET, les valeurs de délai d' `TimeSpan` attente sont spécifiées en tant que valeurs.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-508">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-508">Option</span></span> | <span data-ttu-id="a4e1f-509">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-509">Default value</span></span> | <span data-ttu-id="a4e1f-510">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4e1f-511">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-512">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-512">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-513">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onclose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4e1f-514">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-515">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4e1f-516">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-517">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-518">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-519">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-520">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-520">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-521">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-521">Option</span></span> | <span data-ttu-id="a4e1f-522">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-522">Default value</span></span> | <span data-ttu-id="a4e1f-523">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4e1f-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4e1f-525">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-526">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-526">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-527">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onClose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-528">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-529">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4e1f-530">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-530">15 seconds</span></span> | <span data-ttu-id="a4e1f-531">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-532">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `onClose` l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-533">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-534">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4e1f-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4e1f-536">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-537">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-538">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-539">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4e1f-540">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-540">Configure additional options</span></span>

<span data-ttu-id="a4e1f-541">Des options supplémentaires peuvent être configurées `WithUrl` dans`withUrl` la méthode (en JavaScript `HubConnectionBuilder` ) sur ou sur les différentes API de `HttpHubConnectionBuilder` configuration sur le dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-542">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-543">Option .NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-543">.NET Option</span></span> |  <span data-ttu-id="a4e1f-544">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-544">Default value</span></span> | <span data-ttu-id="a4e1f-545">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-546">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4e1f-547">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-548">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-549">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4e1f-550">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-550">Empty</span></span> | <span data-ttu-id="a4e1f-551">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4e1f-552">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-552">Empty</span></span> | <span data-ttu-id="a4e1f-553">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4e1f-554">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-554">Empty</span></span> | <span data-ttu-id="a4e1f-555">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4e1f-556">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-556">5 seconds</span></span> | <span data-ttu-id="a4e1f-557">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-557">WebSockets only.</span></span> <span data-ttu-id="a4e1f-558">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4e1f-559">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4e1f-560">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-560">Empty</span></span> | <span data-ttu-id="a4e1f-561">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4e1f-562">Délégué qui peut être utilisé pour configurer ou remplacer le utilisé `HttpMessageHandler` pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4e1f-563">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4e1f-564">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4e1f-565">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou `HttpMessageHandler` retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4e1f-566">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="a4e1f-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4e1f-567">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4e1f-568">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4e1f-569">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4e1f-570">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4e1f-571">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-573">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-573">JavaScript Option</span></span> | <span data-ttu-id="a4e1f-574">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-574">Default Value</span></span> | <span data-ttu-id="a4e1f-575">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4e1f-576">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4e1f-577">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-578">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-579">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-580">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-580">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-581">Option Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-581">Java Option</span></span> | <span data-ttu-id="a4e1f-582">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-582">Default Value</span></span> | <span data-ttu-id="a4e1f-583">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-584">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4e1f-585">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-586">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-587">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4e1f-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4e1f-589">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-589">Empty</span></span> | <span data-ttu-id="a4e1f-590">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4e1f-591">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni `WithUrl`à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4e1f-592">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4e1f-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4e1f-593">Dans le client Java, ces options peuvent être configurées avec les méthodes sur `HttpHubConnectionBuilder` le retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4e1f-594">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4e1f-595">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-596">ASP.NET Core Signalr prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4e1f-597">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4e1f-598">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a4e1f-599">`AddJsonProtocol`peut être ajouté après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4e1f-600">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4e1f-601">La propriété [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) sur cet objet est un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objet qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4e1f-602">Pour plus d’informations, consultez la [Documentation System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a4e1f-603">Par exemple, pour configurer le sérialiseur de manière à ce qu’il ne change pas la casse des noms de propriétés, au lieu des noms « la casse mixte » par `Startup.ConfigureServices`défaut, utilisez le code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="a4e1f-604">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4e1f-605">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4e1f-606">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a4e1f-607">Basculer vers Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="a4e1f-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a4e1f-608">Si vous avez besoin de `Newtonsoft.Json` fonctionnalités de qui ne `System.Text.Json`sont pas prises en charge dans, consultez [basculer vers Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4e1f-609">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-609">MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-610">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4e1f-611">Pour plus d’informations, consultez [MessagePack dans signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-612">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4e1f-613">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-613">Configure server options</span></span>

<span data-ttu-id="a4e1f-614">Le tableau suivant décrit les options de configuration des hubs Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4e1f-615">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-615">Option</span></span> | <span data-ttu-id="a4e1f-616">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-616">Default Value</span></span> | <span data-ttu-id="a4e1f-617">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4e1f-618">30 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-618">30 seconds</span></span> | <span data-ttu-id="a4e1f-619">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4e1f-620">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4e1f-621">La valeur recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-622">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-622">15 seconds</span></span> | <span data-ttu-id="a4e1f-623">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4e1f-624">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-625">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-626">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-626">15 seconds</span></span> | <span data-ttu-id="a4e1f-627">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4e1f-628">Lors de `KeepAliveInterval`la modification, `ServerTimeout` / `serverTimeoutInMilliseconds` modifiez le paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4e1f-629">La valeur `ServerTimeout` / `serverTimeoutInMilliseconds` recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4e1f-630">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-630">All installed protocols</span></span> | <span data-ttu-id="a4e1f-631">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-631">Protocols supported by this hub.</span></span> <span data-ttu-id="a4e1f-632">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4e1f-633">Si `true`la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4e1f-634">La valeur par `false`défaut est, car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a4e1f-635">Nombre maximal d’éléments pouvant être mis en mémoire tampon pour les flux de téléchargement du client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a4e1f-636">Si cette limite est atteinte, le traitement des appels est bloqué jusqu’à ce que le serveur traite les éléments de flux.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a4e1f-637">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-637">32 KB</span></span> | <span data-ttu-id="a4e1f-638">Taille maximale d’un seul message de concentrateur entrant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a4e1f-639">Les options peuvent être configurées pour tous les hubs en fournissant un délégué `AddSignalR` d’options `Startup.ConfigureServices`à l’appel dans.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4e1f-640">Les options d’un seul Hub remplacent les options globales `AddSignalR` fournies dans et peuvent être <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4e1f-641">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="a4e1f-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4e1f-642">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4e1f-643">Ces options sont configurées en passant un délégué [à\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) dans. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4e1f-644">Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4e1f-645">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-645">Option</span></span> | <span data-ttu-id="a4e1f-646">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-646">Default Value</span></span> | <span data-ttu-id="a4e1f-647">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4e1f-648">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-648">32 KB</span></span> | <span data-ttu-id="a4e1f-649">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon avant d’appliquer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a4e1f-650">L’augmentation de cette valeur permet au serveur de recevoir plus rapidement des messages plus volumineux sans appliquer la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4e1f-651">Données collectées automatiquement à `Authorize` partir des attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4e1f-652">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4e1f-653">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-653">32 KB</span></span> | <span data-ttu-id="a4e1f-654">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon avant d’observer la contre-pression.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a4e1f-655">L’augmentation de cette valeur permet au serveur de mettre plus rapidement en mémoire tampon des messages plus volumineux sans attendre la contre-pression, mais peut augmenter la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4e1f-656">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-656">All Transports are enabled.</span></span> | <span data-ttu-id="a4e1f-657">Énumération de bits indicateurs `HttpTransportType` des valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4e1f-658">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-658">See below.</span></span> | <span data-ttu-id="a4e1f-659">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4e1f-660">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-660">See below.</span></span> | <span data-ttu-id="a4e1f-661">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a4e1f-662">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4e1f-663">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-663">Option</span></span> | <span data-ttu-id="a4e1f-664">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-664">Default Value</span></span> | <span data-ttu-id="a4e1f-665">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4e1f-666">90 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-666">90 seconds</span></span> | <span data-ttu-id="a4e1f-667">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4e1f-668">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4e1f-669">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées `WebSockets` à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4e1f-670">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-670">Option</span></span> | <span data-ttu-id="a4e1f-671">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-671">Default Value</span></span> | <span data-ttu-id="a4e1f-672">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4e1f-673">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-673">5 seconds</span></span> | <span data-ttu-id="a4e1f-674">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4e1f-675">Délégué qui peut être utilisé pour affecter une valeur `Sec-WebSocket-Protocol` personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4e1f-676">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4e1f-677">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="a4e1f-677">Configure client options</span></span>

<span data-ttu-id="a4e1f-678">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4e1f-679">Il est également disponible dans le client Java, mais la `HttpHubConnectionBuilder` sous-classe est ce qui contient les options de configuration du générateur, ainsi `HubConnection` que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4e1f-680">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="a4e1f-680">Configure logging</span></span>

<span data-ttu-id="a4e1f-681">La journalisation est configurée dans le client `ConfigureLogging` .net à l’aide de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4e1f-682">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4e1f-683">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-684">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4e1f-685">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4e1f-686">Par exemple, pour activer la journalisation de la `Microsoft.Extensions.Logging.Console` console, installez le package NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4e1f-687">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4e1f-688">Dans le client JavaScript, il existe `configureLogging` une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4e1f-689">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4e1f-690">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a4e1f-691">Au lieu d' `LogLevel` une valeur, vous pouvez également fournir `string` une valeur représentant un nom de niveau de journal.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a4e1f-692">Cela est utile lors de la configuration de la journalisation Signalr dans les `LogLevel` environnements où vous n’avez pas accès aux constantes.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a4e1f-693">Le tableau suivant répertorie les niveaux de journalisation disponibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-693">The following table lists the available log levels.</span></span> <span data-ttu-id="a4e1f-694">La valeur que vous fournissez pour `configureLogging` définir le niveau de journalisation **minimale** qui sera enregistré.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a4e1f-695">Les messages enregistrés à ce niveau, **ou les niveaux indiqués après celui-ci dans la table**, sont enregistrés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a4e1f-696">String</span><span class="sxs-lookup"><span data-stu-id="a4e1f-696">String</span></span>                      | <span data-ttu-id="a4e1f-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a4e1f-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a4e1f-698">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a4e1f-699">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a4e1f-700">Pour désactiver entièrement la journalisation `signalR.LogLevel.None` , spécifiez dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4e1f-701">Pour plus d’informations sur la journalisation, consultez la [documentation relative aux diagnostics de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4e1f-702">Le client Java Signalr utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4e1f-703">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4e1f-704">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4e1f-705">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4e1f-706">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4e1f-707">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-707">Configure allowed transports</span></span>

<span data-ttu-id="a4e1f-708">Les transports utilisés par Signalr peuvent être configurés dans l' `WithUrl` appel (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4e1f-709">Une opération or au niveau du bit des `HttpTransportType` valeurs de peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4e1f-710">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-710">All transports are enabled by default.</span></span>

<span data-ttu-id="a4e1f-711">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4e1f-712">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d' `withUrl`options fourni à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4e1f-713">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a4e1f-714">Dans le client Java, le transport est sélectionné à l' `withTransport` aide de la `HttpHubConnectionBuilder`méthode sur le.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a4e1f-715">Le client Java utilise le transport WebSocket par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4e1f-716">Le client Java Signalr ne prend pas encore en charge le transport de secours.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4e1f-717">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-717">Configure bearer authentication</span></span>

<span data-ttu-id="a4e1f-718">Pour fournir des données d’authentification avec les demandes Signalr, `AccessTokenProvider` utilisez l'`accessTokenFactory` option (en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4e1f-719">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP ( `Authorization` à l’aide de l' `Bearer`en-tête de type).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4e1f-720">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4e1f-721">Dans ce cas, le jeton d’accès est fourni sous la forme d' `access_token`une valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4e1f-722">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué `WithUrl`options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4e1f-723">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet `withUrl`d’options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4e1f-724">Dans le client Java Signalr, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4e1f-725">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [RxJava](https://github.com/ReactiveX/RxJava) [>de\<chaîne unique ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4e1f-726">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4e1f-727">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="a4e1f-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4e1f-728">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive `HubConnection` sont disponibles sur l’objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-729">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-730">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-730">Option</span></span> | <span data-ttu-id="a4e1f-731">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-731">Default value</span></span> | <span data-ttu-id="a4e1f-732">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4e1f-733">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-734">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-734">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-735">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `Closed` et déclenche`onclose` l’événement (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-736">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-737">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-738">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-738">15 seconds</span></span> | <span data-ttu-id="a4e1f-739">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-740">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `Closed` l’événement`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-741">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-742">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-743">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-743">15 seconds</span></span> | <span data-ttu-id="a4e1f-744">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-745">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-746">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4e1f-747">Dans le client .NET, les valeurs de délai d' `TimeSpan` attente sont spécifiées en tant que valeurs.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-749">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-749">Option</span></span> | <span data-ttu-id="a4e1f-750">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-750">Default value</span></span> | <span data-ttu-id="a4e1f-751">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4e1f-752">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-753">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-753">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-754">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onclose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4e1f-755">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-756">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4e1f-757">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-758">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-759">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-760">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-761">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-761">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-762">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-762">Option</span></span> | <span data-ttu-id="a4e1f-763">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-763">Default value</span></span> | <span data-ttu-id="a4e1f-764">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4e1f-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4e1f-766">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-767">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-767">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-768">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onClose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-769">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-770">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4e1f-771">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-771">15 seconds</span></span> | <span data-ttu-id="a4e1f-772">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-773">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `onClose` l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-774">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-775">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4e1f-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4e1f-777">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-778">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-779">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-780">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4e1f-781">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-781">Configure additional options</span></span>

<span data-ttu-id="a4e1f-782">Des options supplémentaires peuvent être configurées `WithUrl` dans`withUrl` la méthode (en JavaScript `HubConnectionBuilder` ) sur ou sur les différentes API de `HttpHubConnectionBuilder` configuration sur le dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-783">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-784">Option .NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-784">.NET Option</span></span> |  <span data-ttu-id="a4e1f-785">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-785">Default value</span></span> | <span data-ttu-id="a4e1f-786">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-787">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4e1f-788">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-789">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-790">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4e1f-791">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-791">Empty</span></span> | <span data-ttu-id="a4e1f-792">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4e1f-793">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-793">Empty</span></span> | <span data-ttu-id="a4e1f-794">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4e1f-795">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-795">Empty</span></span> | <span data-ttu-id="a4e1f-796">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4e1f-797">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-797">5 seconds</span></span> | <span data-ttu-id="a4e1f-798">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-798">WebSockets only.</span></span> <span data-ttu-id="a4e1f-799">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4e1f-800">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4e1f-801">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-801">Empty</span></span> | <span data-ttu-id="a4e1f-802">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4e1f-803">Délégué qui peut être utilisé pour configurer ou remplacer le utilisé `HttpMessageHandler` pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4e1f-804">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4e1f-805">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4e1f-806">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou `HttpMessageHandler` retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4e1f-807">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="a4e1f-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4e1f-808">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4e1f-809">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4e1f-810">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4e1f-811">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4e1f-812">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-814">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-814">JavaScript Option</span></span> | <span data-ttu-id="a4e1f-815">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-815">Default Value</span></span> | <span data-ttu-id="a4e1f-816">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4e1f-817">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4e1f-818">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-819">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-820">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-821">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-821">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-822">Option Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-822">Java Option</span></span> | <span data-ttu-id="a4e1f-823">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-823">Default Value</span></span> | <span data-ttu-id="a4e1f-824">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-825">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4e1f-826">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-827">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-828">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4e1f-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4e1f-830">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-830">Empty</span></span> | <span data-ttu-id="a4e1f-831">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4e1f-832">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni `WithUrl`à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4e1f-833">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4e1f-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4e1f-834">Dans le client Java, ces options peuvent être configurées avec les méthodes sur `HttpHubConnectionBuilder` le retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4e1f-835">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4e1f-836">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-837">ASP.NET Core Signalr prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4e1f-838">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4e1f-839">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être `Startup.ConfigureServices` ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a4e1f-840">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4e1f-841">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un objet `JsonSerializerSettings` JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4e1f-842">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a4e1f-843">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le `Startup.ConfigureServices`code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a4e1f-844">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4e1f-845">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4e1f-846">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4e1f-847">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-847">MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-848">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4e1f-849">Pour plus d’informations, consultez [MessagePack dans signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-850">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4e1f-851">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-851">Configure server options</span></span>

<span data-ttu-id="a4e1f-852">Le tableau suivant décrit les options de configuration des hubs Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4e1f-853">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-853">Option</span></span> | <span data-ttu-id="a4e1f-854">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-854">Default Value</span></span> | <span data-ttu-id="a4e1f-855">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4e1f-856">30 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-856">30 seconds</span></span> | <span data-ttu-id="a4e1f-857">Le serveur considère que le client est déconnecté s’il n’a pas reçu de message (y compris Keep-Alive) dans cet intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4e1f-858">Cela peut prendre plus de temps que cet intervalle de délai d’attente pour que le client soit marqué comme déconnecté, en raison de la façon dont il est implémenté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4e1f-859">La valeur recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-860">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-860">15 seconds</span></span> | <span data-ttu-id="a4e1f-861">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4e1f-862">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-863">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-864">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-864">15 seconds</span></span> | <span data-ttu-id="a4e1f-865">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4e1f-866">Lors de `KeepAliveInterval`la modification, `ServerTimeout` / `serverTimeoutInMilliseconds` modifiez le paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4e1f-867">La valeur `ServerTimeout` / `serverTimeoutInMilliseconds` recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4e1f-868">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-868">All installed protocols</span></span> | <span data-ttu-id="a4e1f-869">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-869">Protocols supported by this hub.</span></span> <span data-ttu-id="a4e1f-870">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4e1f-871">Si `true`la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4e1f-872">La valeur par `false`défaut est, car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a4e1f-873">Les options peuvent être configurées pour tous les hubs en fournissant un délégué `AddSignalR` d’options `Startup.ConfigureServices`à l’appel dans.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4e1f-874">Les options d’un seul Hub remplacent les options globales `AddSignalR` fournies dans et peuvent être <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4e1f-875">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="a4e1f-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4e1f-876">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4e1f-877">Ces options sont configurées en passant un délégué [à\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4e1f-878">Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4e1f-879">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-879">Option</span></span> | <span data-ttu-id="a4e1f-880">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-880">Default Value</span></span> | <span data-ttu-id="a4e1f-881">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4e1f-882">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-882">32 KB</span></span> | <span data-ttu-id="a4e1f-883">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a4e1f-884">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4e1f-885">Données collectées automatiquement à `Authorize` partir des attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4e1f-886">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4e1f-887">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-887">32 KB</span></span> | <span data-ttu-id="a4e1f-888">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a4e1f-889">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4e1f-890">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-890">All Transports are enabled.</span></span> | <span data-ttu-id="a4e1f-891">Énumération de bits indicateurs `HttpTransportType` des valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4e1f-892">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-892">See below.</span></span> | <span data-ttu-id="a4e1f-893">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4e1f-894">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-894">See below.</span></span> | <span data-ttu-id="a4e1f-895">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a4e1f-896">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4e1f-897">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-897">Option</span></span> | <span data-ttu-id="a4e1f-898">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-898">Default Value</span></span> | <span data-ttu-id="a4e1f-899">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4e1f-900">90 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-900">90 seconds</span></span> | <span data-ttu-id="a4e1f-901">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4e1f-902">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4e1f-903">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées `WebSockets` à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4e1f-904">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-904">Option</span></span> | <span data-ttu-id="a4e1f-905">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-905">Default Value</span></span> | <span data-ttu-id="a4e1f-906">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4e1f-907">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-907">5 seconds</span></span> | <span data-ttu-id="a4e1f-908">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4e1f-909">Délégué qui peut être utilisé pour affecter une valeur `Sec-WebSocket-Protocol` personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4e1f-910">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4e1f-911">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="a4e1f-911">Configure client options</span></span>

<span data-ttu-id="a4e1f-912">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4e1f-913">Il est également disponible dans le client Java, mais la `HttpHubConnectionBuilder` sous-classe est ce qui contient les options de configuration du générateur, ainsi `HubConnection` que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4e1f-914">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="a4e1f-914">Configure logging</span></span>

<span data-ttu-id="a4e1f-915">La journalisation est configurée dans le client `ConfigureLogging` .net à l’aide de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4e1f-916">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4e1f-917">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-918">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4e1f-919">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4e1f-920">Par exemple, pour activer la journalisation de la `Microsoft.Extensions.Logging.Console` console, installez le package NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4e1f-921">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4e1f-922">Dans le client JavaScript, il existe `configureLogging` une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4e1f-923">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4e1f-924">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4e1f-925">Pour désactiver entièrement la journalisation `signalR.LogLevel.None` , spécifiez dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4e1f-926">Pour plus d’informations sur la journalisation, consultez la [documentation relative aux diagnostics de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4e1f-927">Le client Java Signalr utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4e1f-928">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4e1f-929">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4e1f-930">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4e1f-931">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4e1f-932">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-932">Configure allowed transports</span></span>

<span data-ttu-id="a4e1f-933">Les transports utilisés par Signalr peuvent être configurés dans l' `WithUrl` appel (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4e1f-934">Une opération or au niveau du bit des `HttpTransportType` valeurs de peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4e1f-935">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-935">All transports are enabled by default.</span></span>

<span data-ttu-id="a4e1f-936">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4e1f-937">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d' `withUrl`options fourni à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4e1f-938">Dans cette version du client Java, WebSockets est le seul transport disponible.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4e1f-939">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-939">Configure bearer authentication</span></span>

<span data-ttu-id="a4e1f-940">Pour fournir des données d’authentification avec les demandes Signalr, `AccessTokenProvider` utilisez l'`accessTokenFactory` option (en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4e1f-941">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP ( `Authorization` à l’aide de l' `Bearer`en-tête de type).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4e1f-942">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4e1f-943">Dans ce cas, le jeton d’accès est fourni sous la forme d' `access_token`une valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4e1f-944">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué `WithUrl`options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4e1f-945">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet `withUrl`d’options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4e1f-946">Dans le client Java Signalr, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4e1f-947">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [RxJava](https://github.com/ReactiveX/RxJava) [>de\<chaîne unique ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4e1f-948">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4e1f-949">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="a4e1f-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4e1f-950">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive `HubConnection` sont disponibles sur l’objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-951">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-952">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-952">Option</span></span> | <span data-ttu-id="a4e1f-953">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-953">Default value</span></span> | <span data-ttu-id="a4e1f-954">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4e1f-955">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-956">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-956">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-957">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `Closed` et déclenche`onclose` l’événement (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-958">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-959">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-960">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-960">15 seconds</span></span> | <span data-ttu-id="a4e1f-961">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-962">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `Closed` l’événement`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-963">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-964">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-965">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-965">15 seconds</span></span> | <span data-ttu-id="a4e1f-966">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-967">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-968">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4e1f-969">Dans le client .NET, les valeurs de délai d' `TimeSpan` attente sont spécifiées en tant que valeurs.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-971">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-971">Option</span></span> | <span data-ttu-id="a4e1f-972">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-972">Default value</span></span> | <span data-ttu-id="a4e1f-973">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4e1f-974">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-975">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-975">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-976">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onclose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4e1f-977">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-978">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4e1f-979">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-980">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-981">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-982">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-983">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-983">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-984">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-984">Option</span></span> | <span data-ttu-id="a4e1f-985">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-985">Default value</span></span> | <span data-ttu-id="a4e1f-986">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4e1f-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4e1f-988">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-989">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-989">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-990">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onClose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-991">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-992">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4e1f-993">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-993">15 seconds</span></span> | <span data-ttu-id="a4e1f-994">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-995">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `onClose` l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-996">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-997">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4e1f-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4e1f-999">15 secondes (15 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-1000">Détermine l’intervalle auquel le client envoie des messages ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4e1f-1001">L’envoi d’un message à partir du client réinitialise le minuteur au début de l’intervalle.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4e1f-1002">Si le client n’a pas envoyé de message `ClientTimeoutInterval` dans le jeu sur le serveur, le serveur considère que le client est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4e1f-1003">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1003">Configure additional options</span></span>

<span data-ttu-id="a4e1f-1004">Des options supplémentaires peuvent être configurées `WithUrl` dans`withUrl` la méthode (en JavaScript `HubConnectionBuilder` ) sur ou sur les différentes API de `HttpHubConnectionBuilder` configuration sur le dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-1006">Option .NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1006">.NET Option</span></span> |  <span data-ttu-id="a4e1f-1007">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1007">Default value</span></span> | <span data-ttu-id="a4e1f-1008">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-1009">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4e1f-1010">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-1011">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-1012">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4e1f-1013">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1013">Empty</span></span> | <span data-ttu-id="a4e1f-1014">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4e1f-1015">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1015">Empty</span></span> | <span data-ttu-id="a4e1f-1016">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4e1f-1017">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1017">Empty</span></span> | <span data-ttu-id="a4e1f-1018">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4e1f-1019">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1019">5 seconds</span></span> | <span data-ttu-id="a4e1f-1020">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1020">WebSockets only.</span></span> <span data-ttu-id="a4e1f-1021">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4e1f-1022">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4e1f-1023">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1023">Empty</span></span> | <span data-ttu-id="a4e1f-1024">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4e1f-1025">Délégué qui peut être utilisé pour configurer ou remplacer le utilisé `HttpMessageHandler` pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4e1f-1026">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4e1f-1027">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4e1f-1028">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou `HttpMessageHandler` retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4e1f-1029">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4e1f-1030">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4e1f-1031">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4e1f-1032">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4e1f-1033">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4e1f-1034">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-1036">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1036">JavaScript Option</span></span> | <span data-ttu-id="a4e1f-1037">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1037">Default Value</span></span> | <span data-ttu-id="a4e1f-1038">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4e1f-1039">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4e1f-1040">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-1041">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-1042">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-1043">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-1044">Option Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1044">Java Option</span></span> | <span data-ttu-id="a4e1f-1045">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1045">Default Value</span></span> | <span data-ttu-id="a4e1f-1046">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-1047">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4e1f-1048">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-1049">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-1050">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4e1f-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4e1f-1052">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1052">Empty</span></span> | <span data-ttu-id="a4e1f-1053">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4e1f-1054">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni `WithUrl`à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4e1f-1055">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4e1f-1056">Dans le client Java, ces options peuvent être configurées avec les méthodes sur `HttpHubConnectionBuilder` le retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4e1f-1057">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4e1f-1058">Options de sérialisation JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-1059">ASP.NET Core Signalr prend en charge deux protocoles pour l’encodage des messages : [JSON](https://www.json.org/) et [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4e1f-1060">Chaque protocole possède des options de configuration de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4e1f-1061">La sérialisation JSON peut être configurée sur le serveur à l’aide de la méthode d’extension [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , qui peut être `Startup.ConfigureServices` ajoutée après [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) dans votre méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a4e1f-1062">La `AddJsonProtocol` méthode prend un délégué qui reçoit un `options` objet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4e1f-1063">La propriété [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) sur cet objet est un objet `JsonSerializerSettings` JSON.net qui peut être utilisé pour configurer la sérialisation des arguments et les valeurs de retour.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4e1f-1064">Pour plus d’informations, voir la [documentation sur JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a4e1f-1065">Par exemple, pour configurer le sérialiseur afin d’utiliser les noms de propriété « casse Pascal », au lieu des noms « la casse mixte » par défaut, utilisez le `Startup.ConfigureServices`code suivant dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a4e1f-1066">Dans le client .NET, la même `AddJsonProtocol` méthode d’extension existe sur [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4e1f-1067">L' `Microsoft.Extensions.DependencyInjection` espace de noms doit être importé pour résoudre la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4e1f-1068">Il n’est pas possible de configurer la sérialisation JSON dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4e1f-1069">Options de sérialisation MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1069">MessagePack serialization options</span></span>

<span data-ttu-id="a4e1f-1070">La sérialisation MessagePack peut être configurée en fournissant un délégué à l’appel [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4e1f-1071">Pour plus d’informations, consultez [MessagePack dans signalr](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-1072">Il n’est pas possible de configurer la sérialisation MessagePack dans le client JavaScript pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4e1f-1073">Configurer les options de serveur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1073">Configure server options</span></span>

<span data-ttu-id="a4e1f-1074">Le tableau suivant décrit les options de configuration des hubs Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4e1f-1075">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1075">Option</span></span> | <span data-ttu-id="a4e1f-1076">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1076">Default Value</span></span> | <span data-ttu-id="a4e1f-1077">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-1078">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1078">15 seconds</span></span> | <span data-ttu-id="a4e1f-1079">Si le client n’envoie pas de message d’établissement de liaison initial dans le cadre de cet intervalle de temps, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4e1f-1080">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-1081">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4e1f-1082">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1082">15 seconds</span></span> | <span data-ttu-id="a4e1f-1083">Si le serveur n’a pas envoyé de message dans cet intervalle, un message ping est envoyé automatiquement pour maintenir la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4e1f-1084">Lors de `KeepAliveInterval`la modification, `ServerTimeout` / `serverTimeoutInMilliseconds` modifiez le paramètre sur le client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4e1f-1085">La valeur `ServerTimeout` / `serverTimeoutInMilliseconds` recommandée est le double `KeepAliveInterval` de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4e1f-1086">Tous les protocoles installés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1086">All installed protocols</span></span> | <span data-ttu-id="a4e1f-1087">Protocoles pris en charge par ce concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="a4e1f-1088">Par défaut, tous les protocoles inscrits sur le serveur sont autorisés, mais les protocoles peuvent être supprimés de cette liste pour désactiver des protocoles spécifiques pour des hubs individuels.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4e1f-1089">Si `true`la valeur est, les messages d’exception détaillés sont retournés aux clients lorsqu’une exception est levée dans une méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4e1f-1090">La valeur par `false`défaut est, car ces messages d’exception peuvent contenir des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a4e1f-1091">Les options peuvent être configurées pour tous les hubs en fournissant un délégué `AddSignalR` d’options `Startup.ConfigureServices`à l’appel dans.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4e1f-1092">Les options d’un seul Hub remplacent les options globales `AddSignalR` fournies dans et peuvent être <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configurées à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4e1f-1093">Options de configuration HTTP avancées</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4e1f-1094">Utilisez `HttpConnectionDispatcherOptions` pour configurer les paramètres avancés relatifs aux transports et à la gestion des tampons de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4e1f-1095">Ces options sont configurées en passant un délégué [à\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) dans. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4e1f-1096">Le tableau suivant décrit les options de configuration des options HTTP avancées de ASP.NET Core Signalr :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4e1f-1097">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1097">Option</span></span> | <span data-ttu-id="a4e1f-1098">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1098">Default Value</span></span> | <span data-ttu-id="a4e1f-1099">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4e1f-1100">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1100">32 KB</span></span> | <span data-ttu-id="a4e1f-1101">Nombre maximal d’octets reçus du client que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a4e1f-1102">L’augmentation de cette valeur permet au serveur de recevoir des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4e1f-1103">Données collectées automatiquement à `Authorize` partir des attributs appliqués à la classe de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4e1f-1104">Liste d’objets [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilisés pour déterminer si un client est autorisé à se connecter au concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4e1f-1105">32 Ko</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1105">32 KB</span></span> | <span data-ttu-id="a4e1f-1106">Nombre maximal d’octets envoyés par l’application que le serveur met en mémoire tampon.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a4e1f-1107">L’augmentation de cette valeur permet au serveur d’envoyer des messages plus volumineux, mais peut avoir un impact négatif sur la consommation de mémoire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4e1f-1108">Tous les transports sont activés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1108">All Transports are enabled.</span></span> | <span data-ttu-id="a4e1f-1109">Énumération de bits indicateurs `HttpTransportType` des valeurs qui peuvent restreindre les transports qu’un client peut utiliser pour se connecter.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4e1f-1110">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1110">See below.</span></span> | <span data-ttu-id="a4e1f-1111">Options supplémentaires spécifiques au transport d’interrogation longue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4e1f-1112">Voir ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1112">See below.</span></span> | <span data-ttu-id="a4e1f-1113">Options supplémentaires spécifiques au transport WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a4e1f-1114">Le transport d’interrogation longue dispose d’options supplémentaires qui peuvent être configurées à l’aide de la `LongPolling` propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4e1f-1115">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1115">Option</span></span> | <span data-ttu-id="a4e1f-1116">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1116">Default Value</span></span> | <span data-ttu-id="a4e1f-1117">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4e1f-1118">90 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1118">90 seconds</span></span> | <span data-ttu-id="a4e1f-1119">Durée maximale pendant laquelle le serveur attend qu’un message soit envoyé au client avant de mettre fin à une requête d’interrogation unique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4e1f-1120">Si vous réduisez cette valeur, le client émet de nouvelles demandes d’interrogation plus fréquemment.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4e1f-1121">Le transport WebSocket dispose d’options supplémentaires qui peuvent être configurées `WebSockets` à l’aide de la propriété :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4e1f-1122">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1122">Option</span></span> | <span data-ttu-id="a4e1f-1123">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1123">Default Value</span></span> | <span data-ttu-id="a4e1f-1124">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4e1f-1125">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1125">5 seconds</span></span> | <span data-ttu-id="a4e1f-1126">Une fois le serveur fermé, si le client ne parvient pas à se fermer dans cet intervalle de temps, la connexion est interrompue.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4e1f-1127">Délégué qui peut être utilisé pour affecter une valeur `Sec-WebSocket-Protocol` personnalisée à l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4e1f-1128">Le délégué reçoit les valeurs demandées par le client comme entrée et doit retourner la valeur souhaitée.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4e1f-1129">Configurer les options du client</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1129">Configure client options</span></span>

<span data-ttu-id="a4e1f-1130">Les options du client peuvent être configurées sur le `HubConnectionBuilder` type (disponible dans les clients .net et JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4e1f-1131">Il est également disponible dans le client Java, mais la `HttpHubConnectionBuilder` sous-classe est ce qui contient les options de configuration du générateur, ainsi `HubConnection` que sur le lui-même.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4e1f-1132">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1132">Configure logging</span></span>

<span data-ttu-id="a4e1f-1133">La journalisation est configurée dans le client `ConfigureLogging` .net à l’aide de la méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4e1f-1134">Les fournisseurs de journalisation et les filtres peuvent être enregistrés de la même façon que sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4e1f-1135">Pour plus d’informations, consultez la documentation relative [à la journalisation dans ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4e1f-1136">Pour pouvoir inscrire des fournisseurs de journalisation, vous devez installer les packages nécessaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4e1f-1137">Consultez la section [fournisseurs de journalisation intégrés](xref:fundamentals/logging/index#built-in-logging-providers) de la documentation pour obtenir une liste complète.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4e1f-1138">Par exemple, pour activer la journalisation de la `Microsoft.Extensions.Logging.Console` console, installez le package NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4e1f-1139">Appelez la `AddConsole` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4e1f-1140">Dans le client JavaScript, il existe `configureLogging` une méthode similaire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4e1f-1141">Indiquez une `LogLevel` valeur indiquant le niveau minimal de messages du journal à produire.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4e1f-1142">Les journaux sont écrits dans la fenêtre de la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4e1f-1143">Pour désactiver entièrement la journalisation `signalR.LogLevel.None` , spécifiez dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4e1f-1144">Pour plus d’informations sur la journalisation, consultez la [documentation relative aux diagnostics de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4e1f-1145">Le client Java Signalr utilise la bibliothèque [SLF4J](https://www.slf4j.org/) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4e1f-1146">Il s’agit d’une API de journalisation de haut niveau qui permet aux utilisateurs de la bibliothèque de choisir leur propre implémentation de journalisation spécifique en introduisant une dépendance de journalisation spécifique.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4e1f-1147">L’extrait de code suivant montre comment utiliser `java.util.logging` avec le client Java signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4e1f-1148">Si vous ne configurez pas la journalisation dans vos dépendances, SLF4J charge un journal de non-opération par défaut avec le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4e1f-1149">Cela peut être ignoré en toute sécurité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4e1f-1150">Configurer les transports autorisés</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1150">Configure allowed transports</span></span>

<span data-ttu-id="a4e1f-1151">Les transports utilisés par Signalr peuvent être configurés dans l' `WithUrl` appel (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4e1f-1152">Une opération or au niveau du bit des `HttpTransportType` valeurs de peut être utilisée pour restreindre le client à utiliser uniquement les transports spécifiés.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4e1f-1153">Tous les transports sont activés par défaut.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="a4e1f-1154">Par exemple, pour désactiver le transport des événements envoyés par le serveur, mais autoriser les connexions WebSocket et d’interrogation longue :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4e1f-1155">Dans le client JavaScript, les transports sont configurés en définissant le `transport` champ sur l’objet d' `withUrl`options fourni à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4e1f-1156">Configurer l’authentification du porteur</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1156">Configure bearer authentication</span></span>

<span data-ttu-id="a4e1f-1157">Pour fournir des données d’authentification avec les demandes Signalr, `AccessTokenProvider` utilisez l'`accessTokenFactory` option (en JavaScript) pour spécifier une fonction qui retourne le jeton d’accès souhaité.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4e1f-1158">Dans le client .NET, ce jeton d’accès est transmis en tant que jeton « authentification du porteur » HTTP ( `Authorization` à l’aide de l' `Bearer`en-tête de type).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4e1f-1159">Dans le client JavaScript, le jeton d’accès est utilisé comme jeton du porteur, **sauf** dans certains cas où les API de navigateur restreignent la possibilité d’appliquer des en-têtes (en particulier dans les demandes d’événements envoyés par le serveur et WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4e1f-1160">Dans ce cas, le jeton d’accès est fourni sous la forme d' `access_token`une valeur de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4e1f-1161">Dans le client .NET, l' `AccessTokenProvider` option peut être spécifiée à l’aide du délégué `WithUrl`options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4e1f-1162">Dans le client JavaScript, le jeton d’accès est configuré en définissant le `accessTokenFactory` champ sur l’objet `withUrl`d’options dans :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4e1f-1163">Dans le client Java Signalr, vous pouvez configurer un jeton de porteur à utiliser pour l’authentification en fournissant une fabrique de jetons d’accès au [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4e1f-1164">Utilisez [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) pour fournir une [RxJava](https://github.com/ReactiveX/RxJava) [>de\<chaîne unique ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4e1f-1165">Avec un appel à [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), vous pouvez écrire une logique pour produire des jetons d’accès pour votre client.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4e1f-1166">Configurer les options de délai d’attente et de conservation des connexions</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4e1f-1167">Des options supplémentaires pour configurer le délai d’expiration et le comportement Keep-Alive `HubConnection` sont disponibles sur l’objet lui-même :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-1169">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1169">Option</span></span> | <span data-ttu-id="a4e1f-1170">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1170">Default value</span></span> | <span data-ttu-id="a4e1f-1171">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4e1f-1172">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-1173">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1173">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-1174">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `Closed` et déclenche`onclose` l’événement (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-1175">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-1176">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4e1f-1177">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1177">15 seconds</span></span> | <span data-ttu-id="a4e1f-1178">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-1179">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `Closed` l’événement`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4e1f-1180">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-1181">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a4e1f-1182">Dans le client .NET, les valeurs de délai d' `TimeSpan` attente sont spécifiées en tant que valeurs.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-1184">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1184">Option</span></span> | <span data-ttu-id="a4e1f-1185">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1185">Default value</span></span> | <span data-ttu-id="a4e1f-1186">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4e1f-1187">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-1188">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1188">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-1189">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onclose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4e1f-1190">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-1191">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-1192">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-1193">Option</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1193">Option</span></span> | <span data-ttu-id="a4e1f-1194">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1194">Default value</span></span> | <span data-ttu-id="a4e1f-1195">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4e1f-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4e1f-1197">30 secondes (30 000 millisecondes)</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4e1f-1198">Délai d’expiration de l’activité du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1198">Timeout for server activity.</span></span> <span data-ttu-id="a4e1f-1199">Si le serveur n’a pas envoyé de message dans cet intervalle, le client considère que le serveur est déconnecté `onClose` et déclenche l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-1200">Cette valeur doit être suffisamment grande pour qu’un message ping soit envoyé à partir du serveur **et** reçu par le client dans l’intervalle de délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4e1f-1201">La valeur recommandée est un nombre au moins double de la valeur `KeepAliveInterval` du serveur, pour permettre l’arrivée des commandes ping.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4e1f-1202">15 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1202">15 seconds</span></span> | <span data-ttu-id="a4e1f-1203">Délai d’attente du protocole de transfert initial du serveur.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4e1f-1204">Si le serveur n’envoie pas de réponse de négociation dans cet intervalle, le client annule le protocole de transfert et déclenche `onClose` l’événement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4e1f-1205">Il s’agit d’un paramètre avancé qui ne doit être modifié que si des erreurs de dépassement de délai d’expiration de la liaison se produisent en raison d’une latence importante du réseau.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4e1f-1206">Pour plus d’informations sur le processus d’établissement de liaison, consultez la [spécification du protocole signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4e1f-1207">Configurer des options supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1207">Configure additional options</span></span>

<span data-ttu-id="a4e1f-1208">Des options supplémentaires peuvent être configurées `WithUrl` dans`withUrl` la méthode (en JavaScript `HubConnectionBuilder` ) sur ou sur les différentes API de `HttpHubConnectionBuilder` configuration sur le dans le client Java :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4e1f-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4e1f-1210">Option .NET</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1210">.NET Option</span></span> |  <span data-ttu-id="a4e1f-1211">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1211">Default value</span></span> | <span data-ttu-id="a4e1f-1212">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-1213">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4e1f-1214">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-1215">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-1216">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4e1f-1217">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1217">Empty</span></span> | <span data-ttu-id="a4e1f-1218">Collection de certificats TLS à envoyer aux demandes d’authentification.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4e1f-1219">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1219">Empty</span></span> | <span data-ttu-id="a4e1f-1220">Collection de cookies HTTP à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4e1f-1221">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1221">Empty</span></span> | <span data-ttu-id="a4e1f-1222">Informations d’identification à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4e1f-1223">5 secondes</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1223">5 seconds</span></span> | <span data-ttu-id="a4e1f-1224">WebSocket uniquement.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1224">WebSockets only.</span></span> <span data-ttu-id="a4e1f-1225">Durée d’attente maximale du client après la fermeture du serveur pour accuser réception de la demande de fermeture.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4e1f-1226">Si le serveur n’accuse pas réception de la fermeture dans ce délai, le client se déconnecte.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4e1f-1227">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1227">Empty</span></span> | <span data-ttu-id="a4e1f-1228">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4e1f-1229">Délégué qui peut être utilisé pour configurer ou remplacer le utilisé `HttpMessageHandler` pour envoyer des requêtes http.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4e1f-1230">Non utilisé pour les connexions WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4e1f-1231">Ce délégué doit retourner une valeur non null et il reçoit la valeur par défaut en tant que paramètre.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4e1f-1232">Modifiez les paramètres de cette valeur par défaut et renvoyez-la, ou `HttpMessageHandler` retournez une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4e1f-1233">**Lors du remplacement du gestionnaire, veillez à copier les paramètres que vous souhaitez conserver à partir du gestionnaire fourni. dans le cas contraire, les options configurées (telles que les cookies et les en-têtes) ne s’appliqueront pas au nouveau gestionnaire.**</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4e1f-1234">Proxy HTTP à utiliser lors de l’envoi de requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4e1f-1235">Définissez cette valeur booléenne pour envoyer les informations d’identification par défaut pour les requêtes HTTP et WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4e1f-1236">Cela permet l’utilisation de l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4e1f-1237">Délégué qui peut être utilisé pour configurer des options WebSocket supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4e1f-1238">Reçoit une instance de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) qui peut être utilisée pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4e1f-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4e1f-1240">Option JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1240">JavaScript Option</span></span> | <span data-ttu-id="a4e1f-1241">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1241">Default Value</span></span> | <span data-ttu-id="a4e1f-1242">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4e1f-1243">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4e1f-1244">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-1245">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-1246">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4e1f-1247">Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="a4e1f-1248">Option Java</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1248">Java Option</span></span> | <span data-ttu-id="a4e1f-1249">Valeur par défaut</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1249">Default Value</span></span> | <span data-ttu-id="a4e1f-1250">Description</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4e1f-1251">Fonction qui retourne une chaîne fournie en tant que jeton d’authentification du porteur dans les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4e1f-1252">Affectez la `true` valeur pour ignorer l’étape de négociation.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4e1f-1253">**Pris en charge uniquement lorsque le transport WebSockets est le seul transport activé**.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4e1f-1254">Ce paramètre ne peut pas être activé lors de l’utilisation du service Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4e1f-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4e1f-1256">Vide</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1256">Empty</span></span> | <span data-ttu-id="a4e1f-1257">Mappage d’en-têtes HTTP supplémentaires à envoyer avec chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4e1f-1258">Dans le client .NET, ces options peuvent être modifiées par le délégué d’options fourni `WithUrl`à :</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4e1f-1259">Dans le client JavaScript, ces options peuvent être fournies dans un objet JavaScript fourni à `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4e1f-1260">Dans le client Java, ces options peuvent être configurées avec les méthodes sur `HttpHubConnectionBuilder` le retourné à partir de l’option`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4e1f-1261">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a4e1f-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
